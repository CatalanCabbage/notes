# Software Architecture - Patterns, misc

## Saga design pattern - distributed transactions  
A pattern that provides data consistency across services.  
Basically a sequence of transactions that updates each service and publishes a message or event to trigger the next transaction step.  
If any step fails, it executes compensating transactions that counteract the preceding transactions.  

<img src="https://user-images.githubusercontent.com/45961072/123845302-a92f3400-d931-11eb-867e-1efdee33614a.png" height="350px" width="624px"></img>  

There are 2 implementation approaches:  
* Choreography
* Orchestration

### Choreography
Participants exchange events without a centralized point of control.  
Each local transaction publishes domain events that trigger local transactions in other services.  

<img src="https://user-images.githubusercontent.com/45961072/123845915-5e61ec00-d932-11eb-9c69-fd1a4cec493b.png" height="399px" width="624px"></img>  

**Advantages:**
* Suits simple workflows
* No overhead of a controller
* No single point of failure

**Disadvantages:**
* Workflow can become confusing when adding new steps, as it's difficult to track which saga participants listen to which commands.
* Risk of cyclic dependency between services
* Integration testing becomes harder - all services need to be up

### Orchestration
The centralized controller handles all the transactions and tells the participants which local operations to perform based on events.  
The orchestrator executes saga requests, stores and interprets the states of each task, and handles failure recovery with compensating transactions.

<img src="https://user-images.githubusercontent.com/45961072/123845915-5e61ec00-d932-11eb-9c69-fd1a4cec493b.png" height="399px" width="624px"></img>  

**Advantages:**
* Suits complex workflows
* Has control over all participants' activities, prevents cyclic dependencies
* Separation of concerns - services don't know commands for other participants

**Disadvantages:**
* Additional implementation complexity, overhead
* Additional point of failure

References:  
[MS Azure](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/saga/saga)  
[Microservices.io](https://microservices.io/patterns/data/saga.html)  
[Baeldung](https://www.baeldung.com/cs/saga-pattern-microservices)
