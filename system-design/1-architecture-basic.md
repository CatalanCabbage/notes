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

-----

## Architecture types
### Event-driven architecture
There are generally 2 types of processes in apps: 
- CPU intensive: Computation-heavy like graphics rendering, running ML algorithms or handling data in enterprise systems.
- IO intensive: Messaging, editing or websites.  
**Event-driven architecture is suited for IO intensive applications.**  

In the context of web apps, IO operations are termed as events (clicks, requests, variable value changes, etc).  
A large number of events happening is called a stream of events.  

- Event-driven architecture simply means reacting to the events occurring regularly.  
- Reacts to events as opposed to sequentially moving through lines of code.  
- In order to react to the events, the system has to continually monitor asynchronous data streams.  

<img src="https://user-images.githubusercontent.com/45961072/116735980-e730da00-aa0c-11eb-81d2-9c2a86223ad7.png" height="300px" width="624px"></img>  


> Blocking: Flow of execution of a process is blocked until another process completes its execution.  
> Non-blocking: Asynchronous execution.

Event-driven architecture is also known as non-blocking or reactive architecture.  

**Advantages**:  
- Capable of handling a big number of concurrent connections
- Can scale better
- Provide a more reliable behaviour in a distributed environment
- Enable us to not worry about multi-threads, thread lock, out of memory issues due to high IO etc.

Technologies that implement this: [NodeJS](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/), [Play](https://www.playframework.com/) and [Akka.io](https://akka.io/) in Java  

### Shared nothing architecture
In a distributed system, every module has its own memory, disk, etc and shares nothing with other modules.  
Even if several modules in the system go down, the other modules stay online and unaffected, eliminating single points of failure.  

### Hexagonal Architecture
Three components:
- Ports: APIs - receives data
- Adapters: Converts the data obtained from the Ports, to be processed by Domain
- Domain: Holds business logic

All input goes through ports and adapters; the business logic lies at the centre isolated and all the input and output is at the edges of the structure.  
The goal is to make the app independent, loosely coupled and easy to test - in such a way that it can be tested by humans, automated tests, with mock databases, mock middleware, with or without a UI, without making any changes or adjustments to the code.  