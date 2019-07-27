# Key concept

Based on https://hyperledger-fabric.readthedocs.io/en/release-1.4/key_concepts.html

## What is Hyperledger Fabric?

Where Hyperledger Fabric breaks from some other blockchain systems is that it is **private** and **permissioned**. Rather than an open permissionless system that allows unknown identities to participate in the network, the members of a Hyperledger Fabric network enroll through a trusted **Membership Service Provider (MSP)**.

Hyperledger Fabric also offers the ability to create **channels**, allowing a group of participants to create a separate ledger of transactions. This is an especially important option for networks. ***If two participants form a channel, then those participants — and no others — have copies of the ledger for that channel.***

> Channel로 연결 되면 연결된 노드끼리만 정보 교환 가능

### Shared Ledger

Hyperledger Fabric has a ledger subsystem comprising two components: the **world
state** and the **transaction log**. Each participant has a copy of the ledger to every Hyperledger Fabric network they belong to.

- ***The world state component*** describes the ***state of the ledger at a given point in time.*** It’s the database of the ledger.
- ***The transaction log component*** records all transactions which have resulted in the ***current value of the world state;*** it’s the update history for the world state. The ledger, then, is a combination of the world state database and the transaction log history.

The ledger has a replaceable data store for the world state. By default, this is a LevelDB key-value store database. The transaction log does not need to be pluggable. ***It simply records the before and after values of the ledger database being used by the blockchain network.***

### Smart Contracts

Hyperledger Fabric smart contracts are written in **chaincode** and are invoked by an application external to the blockchain when that application needs to interact with the ledger.

### Consensus

We’ll learn more about the Hyperledger Fabric consensus mechanisms, which currently include SOLO, Kafka, and Raft.

## Hyperledger Fabric Functionalities

### Identity management

To enable permissioned networks, Hyperledger Fabric provides a membership identity service that manages user IDs and authenticates all participants on the network. Access control lists can be used to provide additional layers of permission through authorization of specific network operations. ***For example, a specific user ID could be permitted to invoke a chaincode application, but be blocked from deploying new chaincode***

### Privacy and confidentiality

Private **channels** are restricted messaging paths that can be used to provide transaction privacy and confidentiality for specific subsets of network members. ***All data, including transaction, member and channel information, on a channel are invisible and inaccessible to any network members not explicitly granted access to that channel.***

### Efficient processing

Hyperledger Fabric assigns network roles by node type. ***To provide concurrency and parallelism to the network, transaction execution is separated from transaction ordering and commitment.***

- Executing transactions prior to ordering them enables each peer node to process multiple transactions simultaneously.***(Executing -> Ordering -> Validating)***

This concurrent execution increases processing efficiency on each peer and accelerates delivery of transactions to the ordering service.

In addition to enabling parallel processing, the division of labor unburdens ordering nodes from the demands of transaction execution and ledger maintenance, while peer nodes are freed from ordering (consensus) workloads. This bifurcation(분기점) of roles also limits the processing required for authorization and authentication.

### Chaincode functionality

Chaincode applications encode logic that is invoked by specific types of transactions on the channel. ***Chaincode that defines parameters for a change of asset ownership,*** for example, ensures that all transactions that transfer ownership are subject to the same rules and requirements.

**System chaincode** is distinguished as chaincode that defines operating parameters for the entire channel. ***Lifecycle and configuration system chaincode defines the rules for the channel;*** endorsement and validation system chaincode defines the requirements for endorsing and validating transactions.

### Modular design

Hyperledger Fabric implements a modular architecture to provide functional choice to network designers. Specific algorithms for identity, ordering (consensus) and encryption can be plugged in to any Hyperledger Fabric network. ***The result is a universal blockchain architecture that any industry or public domain can adopt.***

## Hyperledger Fabric Model

