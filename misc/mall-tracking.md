_Mall tracking_ 🛒
# Requirements
## What are we trying to do?
We have a mall with a mesh of CCTVs. We want to track the path people take through the mall. We should be able to specify _filters_ for the characteristics of people whose path data we want to get. Eg: People wearing blue shirts.

## Our assumptions for the system
- Data processing does not happen in real time
- Queries for the data of the last 30 days are allowed

# Design
CCTVs have video data. Video is somehow converted into raw data that we can process.  
This raw data is then transformed into something we can query.  
When we get a query, we try to use the transformed data. If the transformed data does not have the datapoints we need, we trigger the video -> raw data -> transformed data again.  