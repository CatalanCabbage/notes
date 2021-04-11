# Scalability
The ability of an application withstand increased workload without sacrificing latency.  

<img src="https://user-images.githubusercontent.com/45961072/114309649-7b420c80-9b05-11eb-993a-5d0a44e07635.png" height="399px" width="624px"></img>  

---

**Latency:** amount of time a system takes to respond to a user request.  
- **Network Latency**
  - Time taken to send a packet from A to B
  - Boosted using CDNs to deploy servers as close to the end-user as possible
- **Application Latency**
  - Time the application takes to process a request
  - Run stress and load tests, fix bottlenecks

-----

## Vertical scaling
- Adding more power to the hardware.  
- Simplest way to scale; but reaches the maximum possible capacity fairly quickly.  
- Does not contribute to availability
- No need to refactor code
Eg. Increasing RAM or Hard Disk

## Horizontal scaling
- Adding more hardware to the existing hardware pool.  
- Can scale infinitely (reasonably) and dynamically.  
- Increases availability
- Need to refactor code to make it stateless (no static instances, since they're bound to the machine)
Eg. Elastic cloud computing - rent hardware (servers), even with the ability to pay only for use.

-----

Some bottlenecks:
- Single DB (might not be able to handle the load)
- Application logic (lack of asynchronicity, etc. Just...bad code)
- Not caching
- Lack of load balancers
- Lack of data compression

Test and profile apps to find bottlenecks.
