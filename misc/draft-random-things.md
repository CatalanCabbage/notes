> Random things! If "thing" gets too big, group or move it to a separate file.

## Luhn algorithm
Checksum formula used to validate numbers such as IMEI numbers, credit card numbers, etc.  
Note: This does not prevent against malicious attacks, only accidents.  
Steps to compute:  
- General number format: `xxxxxx...c`, where `xxx...` = payload, `c` = check digit
- Drop `c`, take just the payload
- Move from right to left
- Double the value of every second digit starting from the rightmost digit
- Sum the digits of the resulting value in each position
- Sum the resulting values from all positions, say `num`
- Check digit `c = ((10 - num % 10) % 10)`

### Characteristics
- Left padding with `0`s does not affect check value, since it causes no change in position
- Won't detect 2-digit transposition (`09 ↔ 90`), and some twin-errors (`22 ↔ 55, 33 ↔ 66 or 44 ↔ 77`)

### References
https://en.wikipedia.org/wiki/Luhn_algorithm
https://en.wikipedia.org/wiki/Check_digit
-----

## HTTP Status codes
### Ranges
|Range|Type|Example|
|:---:|:---:|:---:|
|1xx|Informational|Protocol switching|
|2xx|Success|OK!|
|3xx|Redirection|Moved permanently|
|4xx|Client error|Not found|
|5xx|Server error|Internal server error|

### Important codes
|Code|Name|TLDR|
|:---:|:---:|:---:|
|100|Continue|Indicates that everything so far is OK, client should continue with the request|
|101|Switching Protocols|Used for protocol upgrades. Eg: [Changing HTTP -> Websocket](https://developer.mozilla.org/en-US/docs/Web/HTTP/Protocol_upgrade_mechanism)|
|200|OK|Request succeeded|
|201|Created|Request succeeded and resource was created|
|202|Accepted|Request received successfully; non-committal|
|204|No content|Request succeeded, but client doesn't need to redirect to some other page. Eg: ["save and continue editing" functionality](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/204)|
|206|Partial Content|Request succeeded; body contains a range of data as requested by the `range` header|
|301|Moved permanently|URL has changed permanently; new URL will be given in the response|
|302|Found|URL has been changed temporarily; client should continue to use this URL|
|304|Not modified|For caching; client can continue using the cached version of the response|
|307|Temporary redirect|Same as `302`, but same HTTP method must be used|
|308|Permanent redirect|Same as `301`, but same HTTP method must be used|
|400|Bad request|Invalid request format, etc|
|401|Unauthorized|Implies that authentication is needed|
|403|Forbidden|Implies that authorization is needed (server knows the identity of client)|
|404|Not found|Resource does not exist|
|405|Method not allowed|Invalid HTTP method used in the request|
|408|Request timeout|Sent when server shuts down an idle client|
|409|Conflict|Request conflicts with the current state of the server|
|411|Length required|`content-Length` is required by the server|
|412|Precondition failed|Client has indicated preconditions that the server doesn't meet|
|413|Payload too long|Request is larger than limits defined by the server|
|414|URI too long|Server fails to interpret the URI due to length|
|415|Unsupported Media Type|Server doesn't support the type sent|
|418|I'm a teapot|The server refuses the attempt to brew coffee with a teapot|
|429|Too Many Requests|Rate limiting|
|500|Internal server error|Unexpected exceptions|
|501|Not implemented|HTTP method isn't supported|
|502|Bad gateway|Server is working as a gateway and got an invalid response|
|503|Service unavailable|Down for maintenance, overloaded and so on|
|504|Gateway timeout|Server is working as a gateway and didn't get a response in time|
|505|HTTP version not supported|Version isn't supported|

### References
[MDN docs](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
# Todoo
- Turing complete  
- Halting problem  
- Byzantine fault tolerance  
- Wash trading  
- RAID drives  
- RAFT  
- Distributed hashing  
- Click-to-run
- Huffman tree
- Cron jobs