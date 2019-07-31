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

### Assets

***Assets can range from the tangible (real estate and hardware) to the intangible (contracts and intellectual property).*** Hyperledger Fabric provides the ability to modify assets using chaincode transactions.

Assets are represented in Hyperledger Fabric as a collection of key-value pairs, with state changes recorded as transactions on a [Channel](https://hyperledger-fabric.readthedocs.io/en/release-1.4/glossary.html#channel) ledger. Assets can be represented in binary and/or JSON form.

You can easily define and use assets in your Hyperledger Fabric applications using the [Hyperledger Composer](https://github.com/hyperledger/composer) tool.

### Chaincode

***Chaincode is software defining an asset or assets, and the transaction instructions for modifying the asset(s).***

- Chaincode enforces the rules for reading or altering key-value pairs or other state database information.
- Chaincode functions execute against the ledger's current state database and are initiated through a transaction proposal.
- Chaincode execution results in a set of key-value writes (write set) that can be submitted to the network and applied to the ledger on all peers.

### Ledger Features

The ledger is the sequenced, tamper-resistant record of all state transitions in the fabric. State transitions are a result of chaincode invocations (‘transactions’) submitted by participating parties. Each transaction results in a set of asset key-value pairs that are committed to the ledger as creates, updates, or deletes.

***There is one ledger per channel.*** Each peer maintains a copy of the ledger for each channel of which they are a member.

### Privacy

***To further obfuscate the data, values within chaincode can be encrypted (in part or in total) using common cryptographic algorithms such as AES before sending transactions to the ordering service and appending blocks to the ledger.*** Once encrypted data has been written to the ledger, it can be decrypted only by a user in possession of the corresponding key that was used to generate the cipher text.

### Consensus

***Consensus is achieved ultimately when the order and results of a block’s transactions have met the explicit policy criteria checks.***

- Prior to commitment, the peers will employ these system chaincodes to ***make sure that enough endorsements are present,*** and that they were derived from the appropriate entities.(잔액 확인)
- Moreover, a ***versioning check*** will take place during which the current state of the ledger is agreed or consented upon, before any blocks containing transactions are appended to the ledger.
  - ***This final check provides protection*** against double spend operations and other threats that might compromise data integrity, and allows for functions to be executed against non-static variables.

Access control lists are implemented on hierarchical layers of the network (ordering service down to channels), and payloads are repeatedly signed, verified and authenticated as a transaction proposal passes through the different architectural components.

To conclude, consensus is not merely limited to the agreed upon order of a batch of transactions; rather, it is an overarching(대단히 중요한) characterization that is achieved as a byproduct of the ongoing verifications that take place during a transaction’s journey from proposal to commitment.

> Hyperledger에서는 endorsement가 coin 같은 개념으로 쓰이는듯?

## Blockchain network

### Creating the Network

***The network is formed when an orderer is started.***

In our example network, N,

- the ordering service comprising a single node, O4, is configured according to a network configuration NC4, which gives administrative rights to organization R4.
- At the network level, ***Certificate Authority CA4 is used to dispense(나누어 주다, 내놓다) identities to the administrators and network nodes of the R4 organization.***

#### Certificate Authorities

You can also see a Certificate Authority, CA4, which is used to issue certificates to administrators and network nodes.

- ***CA4 plays a key role in our network because it dispenses X.509(암호화 알고리즘) certificates that can be used to identify components as belonging to organization R4.***
- Certificates issued by CAs can also be used to sign transactions to indicate that an organization endorses the transaction result – a precondition of it being accepted onto the ledger. Let’s examine these two aspects of a CA in a little more detail.

Firstly, different components of the blockchain network use certificates to identify themselves to each other as being from a particular organization. That’s why there is usually more than one CA supporting a blockchain network – ***different organizations often use different CAs.*** 

We’re going to use four CAs in our network; one of for each organization. Indeed, CAs are so important that Hyperledger Fabric provides you with a built-in one (called *Fabric-CA*) to help you get going, though in practice, organizations will choose to use their own CA.

The mapping of certificates to member organizations is achieved by via a structure called a Membership Services Provider (MSP). Network configuration NC4 uses a named MSP to identify the properties of certificates dispensed by CA4 which associate certificate holders with organization R4.

NC4 can then use this MSP name in policies to grant actors from R4 particular rights over network resources. An example of such a policy is to identify the administrators in R4 who can add new member organizations to the network.

Secondly, we’ll see later how certificates issued by CAs are at the heart of the transaction generation and validation process. Specifically, X.509 certificates are used in client application transaction proposals and smart contract transaction responses to digitally sign transactions.

Let’s recap the basic structure of our example blockchain network.
There’s a resource, the network N, accessed by a set of users defined by a Certificate Authority CA4, who have a set of rights over the resources in the network N as described by policies contained inside a network configuration NC4. ***All of this is made real when we configure and start the ordering service node O4.***

### Adding Network Administrators

Organization R4 updates the network configuration to make organization R1 an administrator too. ***After this point R1 and R4 have equal rights over the network configuration.***

We see the addition of a new organization R1 as an administrator – R1 and R4 now have equal rights over the network. We can also see that certificate authority CA1 has been added – it can be used to identify users from the R1 organization. ***After this point, users from both R1 and R4 can administer the network.***

Although the orderer node, O4, is running on R4’s infrastructure, R1 has shared administrative rights over it, as long as it can gain network access. It means that R1 or R4 could update the network configuration NC4 to allow the R2 organization a subset of network operations. In this way, even though R4 is running the ordering service, and R1 has full administrative rights over it, ***R2 has limited rights to create new consortia.***

### Defining a Consortium

Although the network can now be administered by R1 and R4, there is very little that can be done. ***The first thing we need to do is define a consortium.*** This word literally means “a group with a shared destiny”, so it’s an appropriate choice for a set of organizations in a blockchain network.

***Why are consortia important?***

- We can see that a consortium defines the set of organizations in the network who share a need to **transact** with one another – in this case R1 and R2. It really makes sense to group organizations together if they have a common goal, and that’s exactly what’s happening.

We’re now going to use consortium X1 to create a really important part of a Hyperledger Fabric blockchain – **a channel**.

### Creating a channel for a consortium

There can be multiple channels in a network, but for now, we’ll start with one.

*A channel C1 has been created for R1 and R2 using the consortium definition X1. The channel is governed by a channel configuration CC1, completely separate to the network configuration. CC1 is managed by R1 and R2 who have equal rights over C1. R4 has no rights in CC1 whatsoever.*

***The channel C1 provides a private communications mechanism for the consortium X1.*** We can see channel C1 has been connected to the ordering service O4 but that nothing else is attached to it.

R3 and R4 can only interact with C1 if they are added by R1 or R2 to the appropriate policy in the ***channel configuration CC1.*** An example is defining who can add a new organization to the channel. Specifically, note that R4 cannot add itself to the channel C1 – it must, and can only, be authorized by R1 or R2.

***Why are channels so important? Channels are useful because they provide a mechanism for private communications and private data between the members of a consortium.*** Channels provide privacy from other channels, and from the network. Channels provide an efficient sharing of infrastructure while maintaining data and communications privacy.

We can also see that once a channel has been created, it is in a very real sense “free from the network”. It is only organizations that are explicitly specified in a channel configuration that have any control over it, from this time forward into the future. ***Likewise, any updates to network configuration NC4 from this time onwards will have no direct effect on channel configuration CC1;***

- for example if consortia definition X1 is changed, it will not affect the members of channel C1. Channels are therefore useful because they allow private communications between the organizations constituting the channel.
- Moreover, the data in a channel is completely isolated from the rest of the network, including other channels.

As an aside, there is also a special **system channel** defined for use by the ordering service. It behaves in exactly the same way as a regular channel, which are sometimes called **application channels** for this reason. 

### Applications and Smart Contract chaincode

*A smart contract S5 has been installed onto P1. Client application A1 in organization R1 can use S5 to access the ledger via peer node P1. A1, P1 and O4 are all joined to channel C1, i.e. they can all make use of the communication facilities provided by that channel.*

It might now appear that A1 can access the ledger L1 directly via P1, but in fact, ***all access is managed via a special program called a smart contract chaincode, S5.*** Think of S5 as defining all the common access patterns to the ledger;

- S5 provides a well-defined set of ways by which the ledger L1 can be queried or updated.

 In short, client application A1 has to go through smart contract S5 to get to ledger L1!

### Installing a smart contract

***After a smart contract S5 has been developed, an administrator in organization R1 must install it onto peer node P1.*** This is a straightforward operation; after it has occurred, P1 has full knowledge of S5. Specifically, P1 can see the **implementation** logic of S5 – the program code that it uses to access the ledger L1. We contrast this to the S5 **interface** which merely describes the inputs and outputs of S5, without regard to its implementation.

### Endorsement policy

***The most important piece of additional information supplied at instantiation is an endorsement policy.*** It describes which organizations must approve transactions before they will be accepted by other organizations onto their copy of the ledger. In our sample network, transactions can be only be accepted onto ledger L1 if R1 or R2 endorse them.

