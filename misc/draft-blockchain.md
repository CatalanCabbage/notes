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

Eg: Ethereum, Tezos, Cardano, Eos.io

## Consensus protocols:  
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

## Cryptocurrency
Most popular implementations of Blockchain.  
Mining: _TODO_

## Smart contracts
They are programs stored on a blockchain that run when predetermined conditions are met; it is visible to all users of the blockchain.  
A smart contract fortifies agreements in code so the rules are automatically enforced without courts (or any third party) getting involved.  

Once code is pushed into production, it can no longer be changed by any other means other than a consensus reached through member voting.  
However, this leads to a situation where visible bugs may not be quickly fixed, because any change, even a fix, needs consensus.  
Smart contracts are not legally binding, unlike smart _legal_ contracts. 

**Eg Languages**: Solidity, Simplicity, Scilla, Ivy, Bitcoin Script, Vyper.  
Ethereum is the most popular blockchain to run smart contracts on; they're typically written in [Solidity](https://en.wikipedia.org/wiki/Solidity). Solidity is a turing-complete language that is compiled into low-level bytecode to be run by the Etherium Virtual Machine.  
However, the other languages consider being turing-complete to be a security risk and deliberately avoid them.  

## DAO - Decentralized Autonomous Organization
- A community-led entity with no central authority; no hierarchy.  
- Fully autonomous and transparent: smart contracts lay the foundational rules, execute the agreed upon decisions, and at any point, proposals, voting, and even the very code itself can be publicly audited.  
- To obtain voting power or membership in a DAO, governance tokens (typically cryptocurrencies) are needed.  
- **Alignment of incentives**: It is in the best interest of members to approve proposals that serve the best interests of the protocols itself.  

## NFT - Non-fungible tokens
Non-interchangeable unit of data stored on a blockchain.  
- Differ from cryptocurrencies, since NFTs are uniquely identifiable.  
- Claim to provide a public proof of ownership.
- Do not restrict the sharing or copying of underlying digital files.
- Do not prevent the creation of NFTs with identical associated files.

## Environmental effects

# References:  
[101 Blockchains](https://101blockchains.com/blockchain-explained/) - Very basic  
[Leeway Hertz - Blockchain explained, consensus protocols](https://www.leewayhertz.com/blockchain-technology-explained/)