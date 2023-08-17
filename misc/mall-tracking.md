_Mall tracking_ 🛒
# Requirements
## What are we trying to do?
We have a mall with a mesh of CCTVs. We want to track the path people take through the mall. We should be able to specify _filters_ for the characteristics of people whose path data we want to get. Eg: People wearing blue shirts.

## Our assumptions for the system
- Data processing does not happen in real time
- Queries for the data of the last 30 days are allowed

## What kind of queries do we get?  
Some sample queries we might get: <sup>_#1_</sup>  
- Path of people wearing blue shirts
- Path of people wearing shoes
- Average distance covered by people

# Design
CCTVs have video data. Video is somehow converted into raw data that we can process.  
This raw data is then transformed into something we can query.  
When we get a query, we try to use the transformed data. If the transformed data does not have the datapoints we need, we trigger the video -> raw data -> transformed data again.  

Based on this, we can split it into these sections:  
- Fetching and storing data from CCTV
- Processing videos to get raw data
- Transforming and loading raw data to a data store
- Getting queries from the user
- Resolving queries with data from the data store

Incidental features/nice to haves:
- History and results of past queries
- Metrics (data ingestion, queries processed etc)
- Scheduled reports

# Footnotes
_<sup>#1</sup> Depends on business requirements (the answer to "why do we need this data?"). Will make some assumptions for now._