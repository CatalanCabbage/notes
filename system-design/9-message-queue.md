# Message Queue
A queue which routes messages from the source (producer) to the destination (consumer).  
Generally FIFO; but priority queues handle it differently.  

Features:
- Facilitate asynchronous behaviour: Modules can communicate in the background without blocking primary tasks.
- Facilitate cross-module communication: Allows communication in a heterogeneous environment, and also provide temporary storage until messages are processed.

Eg. RabbitMQ, Apache ActiveMQ, Apache Kafka, Azure Event Hubs, AWS SNSS

Protocols: Advanced Message Queuing Protocol (AMQP), Streaming Text Oriented Messaging Protocol (STOMP)

## Publish-Subscribe (Pub-Sub) Model
A method of message transfer where the publisher doesn’t need to know who uses the broadcasted information, and the subscribers don’t need to know who the message comes from.  

- Publishers publish messages to exchanges 
- Exchanges distribute message copies to queues using rules, called bindings
- Subscribers get messages from queues

<img src="https://user-images.githubusercontent.com/45961072/115385203-80821400-a1f5-11eb-88af-a00276116e07.png" height="350px" width="300px"></img>  

Note: Specifics are taken from [_RabbitMQ_](https://www.rabbitmq.com/tutorials/amqp-concepts.html), but can be generalized.

### Exchanges 
Push the messages from Publishers to the queues based on the exchange type and bindings.  

Exchanges can be durable or transient.  
Durable exchanges survive broker restart whereas transient exchanges do not (they have to be redeclared when broker comes back online).  
Not all scenarios and use cases require exchanges to be durable.

Exchanges are declared with a number of attributes in RabbitMQ, can be extended to all:
- Name
- Durability (exchanges survive broker restart)
- Auto-delete (exchange is deleted when last queue is unbound from it)
- Arguments (optional, used by plugins and broker-specific features)

Types of Exchanges:
- Direct exchange
- Fanout exchange
- Topic exchange
- Headers exchange

#### Direct exchange:
**Delivers messages to queues bound to it based on the message routing key.**  
- A queue binds to the exchange with a routing key `pdf_create`
- When a new message arrives at the direct exchange, the exchange routes it to the queue only if `new_message_key == pdf_create`  

<img src="https://user-images.githubusercontent.com/45961072/115385439-c5a64600-a1f5-11eb-8eda-9e488307a634.png" height="350px" width="300px"></img>  

#### Fanout exchange:
**Routes messages to all of the queues bound to it**. The routing key is ignored.  

Use-cases:  
- MMORPG (update leaderboards, etc)
- Sports sites (broadcast live score)
- Distributed systems (broadcast state or config changes)

<img src="https://user-images.githubusercontent.com/45961072/115385487-d2c33500-a1f5-11eb-8024-2910f83e2a2f.png" height="350px" width="300px"></img>  

#### Topic exchange:
Routes messages to queues based on pattern-matching between a message routing key and the pattern that was used to bind a queue to an exchange.  
Offers more fine-grained control of the messages received in a queue.  
Used when the system involves multiple consumers/applications that selectively choose which type of messages they want to receive.  

Use-cases:
- Task processing where each consumer performs a subset of tasks
- Distribution of data relevant to geographic locations
- News updates filtered by category

<img src="https://user-images.githubusercontent.com/45961072/115385402-b9ba8400-a1f5-11eb-9096-0917a6c9089c.png" height="350px" width="300px"></img>  


#### Headers exchange:
Designed for routing on multiple attributes that are more easily expressed as message headers than a routing key.  
Message is routed to a queue only if Headers matched the routing key of the queue.  
Basically direct exchange, but logic can be specified (must match all headers, or one header, etc)

<img src="https://user-images.githubusercontent.com/45961072/115385537-df478d80-a1f5-11eb-9c70-e3513fd1ba61.png" height="350px" width="300px"></img>  

## Point to Point Model
Message from a producer is consumed only by message receiver (which may be streamed to more than one consumer)  

Two variants:
- Fire and forget: Just sends the message, does not care about if it was received or not
- The request/reply model: Sends the message and waits for the response.
