# Software Architecture - Basic

## Tiers
An application might have various _Tiers_.  
**Tier**: Logical separation of components (not code layers!).  
**Components**: Database, User interface, App server (backend), Messaging, Caching, etc.


### Single Tier:
<img src="https://user-images.githubusercontent.com/45961072/114297126-83318a80-9acc-11eb-9f85-dbe81ba6bf3a.png" height="399px" width="624px"></img>

|What?|TLDR|Examples|
|:---:|:---:|:---:|
|Basically onPremise products.|UI, backend, DB - all components on the same machine.|PC Games, LibreOffice suite|

**Advantages**
- No network latency
- Data is quickly available
- Data safety does not depend on network 

**Disadvantages**
- The business has no control over the application
- Code can be reverse engineered
- Performance and look & feel are not consistent over all machines

### Two Tier:
<img src="https://user-images.githubusercontent.com/45961072/114297187-ce4b9d80-9acc-11eb-8ad3-2526f8c99a03.JPG" height="399px" width="624px"></img>


|What?|TLDR|Examples|
|:---:|:---:|:---:|
|Client - DB Server model|2 machines: Client (UI + Business logic), Server (DB)|Heavy mobile games, to-do lists|

**Disadvantages**: Same as previous.

So why is this needed?  
- Keeps latency low since business logic and UI are in the same machine
- Network call to server (DB) only when data needs to be persisted
- Economical (fewer calls)

Eg., in games: 
- Low latency: The files are downloaded onto the client. 
- Fewer server calls: Calls are made only to keep the game state persistent.

### Three Tier:
<img src="https://user-images.githubusercontent.com/45961072/114297209-f2a77a00-9acc-11eb-8dea-42c9084ca9d0.png" height="399px" width="624px"></img>


|What?|TLDR|Examples|
|:---:|:---:|:---:|
|Client - Server - DB model|Client, backend server and DB on separate machines.|Blogs, websites|

  
Something like HTML-CSS-xJS + Apache/NGINX + PostgreSQL.  
Most popular, suits many basic applications; business can control the logic and data.

### N-Tier:
More components involved, such as:  
- Cache
- Message queues for asynchronous behaviour
- Load balancers
- Search servers for searching through massive amounts of data
- Components involved in processing massive amounts of data
- [Web services](https://www.tutorialspoint.com/webservices/what_are_web_services.htm)

-----

### Why are so many Tiers needed?
- **Single Responsibility Principle**: Giving one, just one responsibility to a component and let it execute it with perfection. 
- **Separation of Concerns**: Be concerned about your work only and stop worrying about the rest of the stuff.

**Advantages**
- More flexibility, easier management (can upgrade/change any single component easily)
- No single point of failure (does not depend on a single machine)
- Low dependency, loosely coupled - helps scaling later
