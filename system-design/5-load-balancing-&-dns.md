# Load Balancing & DNS
Load balancers 
- Routinely check the health of components
- Distribute heavy traffic load across the components (like servers) running in the cluster
- Reduce the risk of high load on a few machines
- Act as a single point of contact to clients

They can be used to manage traffic directed towards any component.  

<img src="https://user-images.githubusercontent.com/45961072/114415360-3bdef300-9bcd-11eb-89d1-3d889aec95d2.png" height="399px" width="684px"></img>

> In-service instances: Instances (like servers) that are up and running in the cluster  
> Out-of-service instances: Instances that are down

## Load Balancing methods
- DNS Load Balancing
- Hardware-based Load Balancing 
  - Network switches and routers - Cisco Catalyst, Barracuda load balancer
- Software-based Load Balancing
  - Work on scheduling algorithms - HAProxy, NGINX

## Algorithms used in Load Balancing
- Round Robin/Weighted Round Robin: Routed sequentially to servers.
- Least connections: Routed to servers with the least open connections.
- Random
- Hash: Routes to a specific server based on source IP Hash.
  - A client will always connect to the same server.
  - Can provided efficient ways to implement caching.

---

# DNS
Every machine that's online has a unique IP address (2001:db8:0:1194:0:567:8:1).  
DNS maps IP addresses to Domain names (www.example.com).

Components:
- DNS Resolver (DNS Recursive Nameserver) 
- Root Nameserver
- Top-Level Domain Nameserver
- Domain Nameserver (Authoritative Nameserver)

## DNS Resolution (DNS query lookup)
```User enters domain name -> DNS Resolver -> Root Nameserver -> TLD Nameserver -> Domain Nameserver (Authoritative Nameserver) -> Domain IP address```

Most of this DNS information is cached based on a TTL, usually a few hours.

<img src="https://user-images.githubusercontent.com/45961072/114466792-8f216780-9c06-11eb-9640-26ddedc3155f.png" height="399px" width="684px"></img>

## DNS Load Balancing - Authoritative Nameserver
- Domains have multiple servers in clusters spread out over various data centres.  
- Hence, the Authoritative Nameserver returns a *list of IP addresses* instead of a single address.  
- With every request, the authoritative server changes the order of the IP addresses in the list in a round-robin fashion.  
- The client forwards requests to the first address on the list.
