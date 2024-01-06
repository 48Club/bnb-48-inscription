V1.1.0
Jan 6 2024

# Standards of bnb-48 Inscription

## Introduction:
The process of inscription involves a sequence of commands affixed to transactions. 

The bnb-48 inscription standard ensures a structured and identifiable format for commands attached to transactions on any block chain. The integration of these specifications aims to provide clarity and coherence in the execution of operations.


## Main Features
1. Block Chain Transparency. bnb-48 standard is suitable for any blockchain, as far as transactions on this chain supports calldata/memo/remark etc. It's super easy for indexers to track bnb-48 inscription even across different blockchains.
2. Unlike an NFT standard, bnb-48 is not designed for non-fungible tokens. This design choice facilitates native bulk transfers.
3. Providing functionality interfaces similar to ERC-20, these interfaces form the foundational infrastructure for DeFi.
4. Multi commands are allowed to be carried in a single tx, ensuring a cost-effective and efficient process.
5. Multiple serialization formats support. Developer is free to choose read-freindly format like json or efficiency orientation format like protobuffer.
6. Backward compatible upgradability.


Notably, bnb-48 serves as a standard for inscription originated on the BNB Smart Chain, with '48' denoting its association with the 48 Club.

## OP Sections
## Token OP Commands
## NFT OP Commands
## Social OP Commands
# For Indexer
## Snapshot
### fans
