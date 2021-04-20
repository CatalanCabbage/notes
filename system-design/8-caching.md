# Caching
A way to ensure low latency and high throughput by using a high-speed data storage layer which stores a subset of data, typically transient in nature, so that future requests for that data are served up faster than is possible by accessing the data’s primary storage location.  
Caching allows you to efficiently reuse previously retrieved or computed data.  

A cache's primary purpose is to increase data retrieval performance by reducing the need to access the underlying slower storage layer, and the tradeoff is usually capacity for speed.  

> Cache Invalidation: The process where data is purged from the cache and is replaced by newly updated data after the TTL of the old data is reached.

Caching can be implemented at any layer: Browser cache, [HTTP caching](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching), Database caching, DNS caching, etc  
