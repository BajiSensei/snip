*Note: Taken from Secret Graypaper and Figment.io's learn Secret Pathway*

### Notes on the Secret Network

**Summary:** Secret Network allows for programmable privacy. When users send data, it is sent via encryption to a bunch of secret nodes, which perform computation on the data. 

**Introduction**: Blockchains are a decentralized ledger that store transaction in Blocks, which is cryptographically immutable. Permisionless networks such as Cosmos, are open-for-participation. This means that while anyone can join and use the network, it also means that anyone can access ledger data using block explorers such as [Mintscan](https://www.mintscan.io/cosmos). This is not completely identifiable, but is pseudononymous - which means that using a certain set of tools, some addresses face the risk of identity exposure. 

**Rough Architecture of Secret Network**

Secret Network (SN) is built using the Cosmos SDK and uses Tendermint's BFT consensus algorithm. Computations are performed by each node on the network, thus providing security (explained later). Secret is also IBC-enabled.

SN uses TEEs or Trusted Execution Environments to securely compute smart contracts on the nodes. TEEs ensure that data is stored, processed and protected in an environment that cannot be tampered.

SN guarentees that nodes are unable to peek computations that occur within the TEE - however the underlying ledger is publicly visible. Validators earn rewards from tx_fees and block rewards. 

New nodes on the network are verified using remote attestation and TEEs are implented using [Intel SGX](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions.html). There is a 3-step process for adding a new node to the network (taking from official docs)- 

1. Remote Attestation, 

2. The node generated a random 256 bit number known as the consensus seed. The consensus seed is the most critical part of the Secret Network encryption schema as all other keys and therefore functionality of the protocol are contingent upon secure distribution of this originally generated consensus seed. Using HKDF-SHA256 the consensus seed, in combination with other context-relevant data, derived private keys for the process of registering a new node, I/O encryption, and state encryption. New nodes also use HKDF-SHA256 for key derivation using the original seed or second-generation seeds. Then, the consensus seed is sealed to the disk of the bootstrap node at $HOME/.sgx_secrets/consensus_seed.sealed

3. The remote attestation proof, the public key for the consensus seed exchange, and the public key for the consensus I/O exchange are all published to the Secret Network genesis.json. Curve25519 is the elliptic curve used for asymmetric key generation and ECDH (x25519) is used for deriving symmetric encryption keys which are used to encrypt data with AES-128-SIV.

<hr>

### SNIP721 

We will be going through the code in `/src`. The order is all follows-
1. `connect.js`
2. `create_account.js`
3. `query.js`
4. `transfer.js`

_______

#### Connecting to a secret node using DataHub