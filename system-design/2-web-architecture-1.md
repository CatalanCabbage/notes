# Web architecture - Client/Server
<img src="https://user-images.githubusercontent.com/45961072/114298081-2afd8700-9ad2-11eb-8ac1-6a7e05692595.png" height="399px" width="624px"></img>  
_Typical architecture_

Works on a request-response model.  
Client sends the request to the server for information and the server responds with it.  
_(there's also the P2P model)_

## Client
Holds the UI; takes care of the presentation.
- Thin Client: Has the UI, no business logic at all.
- Thick client: Has the UI and all or some part of the logic (like 2-tier)

## Server
Receives requests from the client, performs business logic, provides a response.  
Some kinds:
- Application server (runs web apps)
- [Proxy server](https://en.wikipedia.org/wiki/Proxy_server) (intermediary; adds structure and encapsulation to the architecture)
- Mail server
- File server
- Virtual server

## Client-Server communication
### REST/SOAP APIs
Happens over the [HTTP protocol](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview), via REST API.
REST API is:
- Stateless (every request is like a new request - needs to have auth info, etc)
- Client agnostic (since response is usually in JSON, any client can reuse endpoints)

Before REST, client-server were tightly coupled; eg. JSPs.
- Business logic was in JSP tags
- Different code needed for different clients

### Remote Procedural Calls (RPC)
This model tries to simulate the calling of a local function from the same process even when request is initiated from outside the process.  
This is called **Location Abstraction**.  

However, a remote service is fundamentally different from a local process. This dissonance leads to some flaws:  
- Network failures need to be anticipated. A local function call is predictable and either succeeds or fails; however, a network call may fail due to parameters outside our control.
- A network call might never return and be lost due to timeouts - we have no way of knowing if a request got through or not
- Idempotence needs to be built into functions. Networks might retry the same action multiple times - say the request goes through and the function is executed, but the response is lost.
- Local references can be passed efficiently in local functions. In RPC, all params need to be passed through as a sequence of bytes. It can get complicated with complex objects.
- Low interoperability - the client and server might be written in different languages. One language might not even support the datatype present in another language.



---

## Modes of data transfer
- HTTP Pull (client keeps pinging server)
- HTTP Push (client pings server once, server keeps sending data)

<img src="https://user-images.githubusercontent.com/45961072/114303155-4b394000-9aea-11eb-962a-576bff5aee09.png" height="399px" width="624px"></img>

### HTTP Pull
Client **pulls the data** from the server whenever it requires updated data.  
It keeps doing it over and over to fetch the updated data.  
Eg. To display non-critical stock prices - updated every x seconds.  
**Disadvantage:** When there's no updated data on the server, the client's requests are wasted.

Types:
- Normal HTTP GET requests
- Polling with AJAX

#### Normal HTTP GET requests
Usually triggered by manual actions such as clicking a button, etc.

#### AJAX
Asynchronous JavaScript & XML.  
Client automatically sends the requests over and over at stipulated intervals to fetch updated data from the server.  
On receiving an update, the client updates the presentation without the need to reload the page.  

> Polling: The dynamic technique of requesting information from the server after regular intervals.

### HTTP Push 
In HTTP Push, the client sends the request for particular information to the server for the first time, and then the server keeps pushing the new updates to the client whenever they are available.  
Eg. Online games - large volume of communication, better to keep them open than to create new requests every time.   

**Advantage:** Saves bandwidth since requests are not wasted when there are no updates.  
**Disadvantage:** Resource intensive.  

For this method, a persistent connection is needed between the Client and the Server.  
> Persistent connection: A network connection between the client and the server that remains open for further requests & responses, as opposed to being closed after a single piece of communication.

> TTL (Time to Live): every request has a specified timeout, usually 30s - 60s.
> If the client does not receive a response from the within TTL, it kills the connection.
 
To hold a persistent connection, the connection needs to stay open for longer than the TTL; to do this, Heartbeat Interceptors are used.  
> Heartbeat Interceptors: Blank request-responses between the client and the server to keep the connection open and prevent the browser from killing it.

Some types:
- Web Sockets
- Ajax Long polling
- HTML5 Event Source API & Server Sent Events 
- Streaming over HTTP

#### Web Sockets
- Provides a **persistent bi-directional low latency** data flow between the client and server.
- Uses HTTP as the initial transport mechanism, but keeps the TCP connection alive after the HTTP response is received so that it can be used for sending messages.
- So technically, transfer happens over TCP, not HTTP.

Use cases: Messaging apps, MMORPG

#### Ajax Long polling
Instead of immediately returning the AJAX response, the server holds the response until it finds an update to be sent to the client.  
If the connection breaks, connection needs to be re-established.  
Used in simple async data fetch cases when update count is too low to warrant a Web Socket.

#### HTML5 Event Source API & Server Sent Events 
Client establishes the connection with an initial request, after which servers can initiate **unidirectional data transmission** towards the client.  
Further incoming messages from the server are treated as Events.  
[MDN docs](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events)

Use-cases: Updating feeds in Social Media apps

#### Streaming over HTTP
Used for streaming multimedia content over HTTP.  
This makes it possible to watch videos as they buffer (~get data from the server)

Data is split into tinier **chunks** and sent through a **stream**; the chunks placed in a stream are said to be **enqueued**.  
These chunks are read by a **reader** that processes data a chunk at a time; the reader along with other processing code are called the **consumer**.  
Each reader also has a **controller** that allows us to control (close, etc) the stream.  
Only one reader can read a stream at a time; the reader is **locked** to the stream.  
However, it is possible to split a stream into two identical copies, which can then be read by two separate readers. This is called **teeing**.  
[MDN docs, great article](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API/Concepts)

Use-cases: Video streaming.

