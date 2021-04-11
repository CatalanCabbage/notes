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
- Data safety - no network loss

**Disadvantages**
- The business has no control over the application
- Code can be reverse engineered
- Performance and look & feel are not consistent over all machines
