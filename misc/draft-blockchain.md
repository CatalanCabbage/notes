# Blockchain
A peer-to-peer system of storing data chronologically where it's practically impossible to tamper it; all members can verify the integrity of the data.  


A blockchain is a linear linked-list of blocks, where each block is connected to the previous block.  
The blocks contain these components:  
- **Data:** Information to be stored
- **Hash:** Unique identifier of the block
- **Hash of the previous block:** To identify the previous block
- **Metadata:** Data like timestamp

The first block is called the **Genesis block**.

Characteristics of blockchain:  
- **Distributed:** All members have a copy of the ledger
- **Decentralized:** No single member has control
- **Immutable:** Every change is permanent
- **Integrity by consensus:** Does not require all nodes to be trusted nodes like traditional networks  

Consensus protocols:  
Proof of Work (PoW)
Proof of Stake (PoS) 
Delegated Proof of Stake (DPoS)
Proof of Elapsed Time (PoET)
Proof of Importance (PoI)
Proof of Capacity (PoC)
Proof of Weight (PoWeight)
Proof of Activity (PoA)
Practical Byzantine Fault Tolerance (PBFT)
Simplified Byzantine Fault Tolerance (SBFT)
Directed Acyclic Graphs (DAG)

The 51% attack: The Blockchain's blocks are built on consensus by more than 50% of members.  
When a single party attains more than 50% of a blockchain's mining power, they can take over the ledger and manipulate it.  
However:
- It would be expensive for attackers to acquire enough mining power
- Individuals would exit the digital currency if a single party started buying everything
- Price would ramp up to prevent a hostile takeover
- Attacking the network would decrease the coins’ value that the attacker is holding

# Cryptocurrency
Most popular implementations of Blockchain.  
Mining:



# References:  
[101 Blockchains](https://101blockchains.com/blockchain-explained/) - Very basic  
[Leeway Hertz - Blockchain explained, consensus protocols](https://www.leewayhertz.com/blockchain-technology-explained/)