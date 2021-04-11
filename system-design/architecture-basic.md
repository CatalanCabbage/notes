# Sofware Architecture - Basic

## Tiers
An application might have various _Tiers_.  
**Tier**: Logical separation of components (not code layers!).  
**Components**: Database, User interface, App server (backend), Messaging, Caching, etc.

### Single Tier:
All components reside on the same machine (basically onPremise products).  
Eg: PC Games, LibreOffice suite.  
The User interface, Database and backend all reside on the same machine.  

**Advantages**
- No network latency
- Data is quickly available
- Data safety does not depend on network 

**Disadvantages**
- The business has no control over the application
- Code can be reverse engineered
- Performance and look & feel are not consistent over all machines

### Two Tier:
Client and server.  
Client has UI and business logic and is in one machine; the server has the DB alone, on another machine.
Eg: Mobile games, To-do lists.

**Disadvantages**: Same as previous.

So why is this needed?  
- Keeps latency low since business logic and UI are in the same machine
- Network call to server (DB) only when data needs to be persisted
- Economical (fewer calls)

Eg., in games: 
- Low latency: The files are downloaded onto the client. 
- Fewer server calls: Calls are made only to keep the game state persistent.










