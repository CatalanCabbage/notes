> Random things! If "thing" gets too big, group or move it to a separate file.

# Random stuff
### Luhn algorithm
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

#### Characteristics
- Left padding with `0`s does not affect check value, since it causes no change in position
- Won't detect 2-digit transposition (`09 ↔ 90`), and some twin-errors (`22 ↔ 55, 33 ↔ 66 or 44 ↔ 77`)

https://en.wikipedia.org/wiki/Luhn_algorithm
https://en.wikipedia.org/wiki/Check_digit



# Todoo
- Turing complete  
- Halting problem  
- Byzantine fault tolerance  
- Wash trading  
- RAID drives  
- RAFT  
- Distributed hashing  
- Click-to-run