_Mall tracking_ 🛒
# Requirements
## What are we trying to do?
We have a mall with a mesh of CCTVs. We want to track the path people take through the mall. We should be able to specify _filters_ for the characteristics of people whose path data we want to get. Eg: People wearing blue shirts.

## Our assumptions for the system
- Data processing does not happen in real time
- Queries for the data of the last 30 days are allowed

## What kind of queries do we get?  
Some sample queries we might get:[^queries]  
- Path of people wearing blue shirts
- Path of people wearing shoes
- Average distance covered by people

# Design
## Workflows
**Population and extraction flow:**    
CCTVs have video data. Video is somehow converted into raw data that we can process.  
This raw data is then transformed into something we can query.  
**User querying flow:**  
When we get a query, we try to use the transformed data. If the transformed data does not have the datapoints we need, we trigger the video -> raw data -> transformed data again.  

## Component discussions
Based on this, we can split it into these sections:  
1. Fetching and storing data from CCTV
1. Processing videos to get raw data
1. Transforming and loading raw data to a data store
1. Getting queries from the user
1. Resolving queries with data from the data store

_(todo)_ Incidental features/nice to haves:
- History and results of past queries
- Metrics (data ingestion, queries processed etc)
- Scheduled reports

### Fetching and storing data from CCTV
**1. How much video do we have?**  
Factors: Number of CCTVs (1000), retention period (30 days + 30 days backup), quality (HD, 1080p).  
Storage needed for 60 days[^worst-case]: `1.2GB (1 hour HD video) * 1000 CCTVs * 24 hours * 60 days` = `1728TB`.

**2. Where do we store videos?**  
For data of the first 30 days, store it in warm storage like S3. For data of the next 30 days, store it in cold storage with expected infrequent access like Glacier (S3 costs >4x more than Glacier!).

**3. How do we get data from CCTVs to the data store?**  
Bandwidth needed per second: `1.2GB (per hour) * 1000 CCTVs / 60` = `20GB/s`.  
An on-prem server is needed to reliably take data from the CCTVs and upload it to the data store.  

#### Conclusion:
- An on-prem server is present in the mall to reliably upload video data to our data store.  
- We have warm storage for 30 days of video data and cold storage for the next 30 days.

_Scope for optimization: We can move some of the basic processing from our processing load to this on-prem server; will reduce the size of data sent and load of the system._

### Processing videos to get raw data
In this context, raw data is the output from the ML model. Maybe we need to do some extra processing to make it usable, split it into floor sections etc later.  

1. **What is the scale of this data, how expensive is the processing?**  
Scale is 28tb/day; processing depends on the application, but from a structural pov looks like it would take a few servers - the equivalent of EC2 instances.[^ec2-for-ml]

**2. What does the raw data look like?**
A unique identifier, a list of co-ordinates, attributes, timestamps in any format.  

**3. When is data purged?**  
A cron job/scheduler can be set up to move data from warm storage to cold storage everyday.

### Conclusion:
We have a few servers that process video data and convert it into raw data, and a cron job that runs to move data to cold storage past the 30 day mark.

### Transforming and loading raw data to a data store
**What does the data in the data store look like?**


**What is the scale of this data?**    

**When is data purged?**  


### Resolving queries with data from the data store
- What happens when data not present in the data store is requested?
- When is data purged?



# Footnotes
[^queries]: _Depends on business requirements (the answer to "why do we need this data?"). Will make some assumptions for now._  
[^worst-case]: _Assuming all CCTVs are recording 24 hours a day. Practically we might have off hours where analytics isn't needed._
[^ec2-for-ml]: _[Amazon's ML infrastructure recommmendation](https://aws.amazon.com/machine-learning/infrastructure-innovation), a bunch of EC2 instances._