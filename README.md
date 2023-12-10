# Standards of bnb-48 Inscription

## Introduction:
The process of inscription involves a sequence of commands affixed to transactions in hexadecimal format. Notably, bnb-48 serves as a standard for inscription on the BNB Smart Chain, with '48' denoting its association with the 48 Club.

The bnb-48 inscription standard ensures a structured and identifiable format for commands attached to transactions on the BNB Smart Chain. The integration of these specifications aims to provide clarity and coherence in the execution of operations.

## Main Features

1. No pre-authorization is required; the system is open for anyone to issue inscriptions following the bnb-48 standard.
Implication: This approach fosters inclusivity, allowing a wide range of participants to seamlessly engage in the inscription process.
2. Unlike an NFT standard, bnb-48 is not designed for non-fungible tokens. This design choice facilitates bulk transfers, ensuring a cost-effective and efficient process.
Community-Orientation:
3. Providing functionality interfaces similar to ERC-20, these interfaces form the foundational infrastructure for DeFi.
4. Community activities can be directly reflected through the bnb-48 standard. Furthermore, the standard is designed to accommodate evolution, allowing for backward-compatible upgrades. This feature enables continuous improvement while maintaining compatibility with existing implementations.


## Agreement:
The call data format typically consists of an unmarched JSON following the predetermined string `data:,`

An example:
```
data:,{"p":"bnb-48","op":opvalue ... "tuple":tuplevalue}
```

When asset movement is involved, the sender (if any) will always be the sender of the carrier transaction, and the receiver will always be the receiver of the carrier transaction. This rule makes sure bnb-48 standard is secured by the blockchain intrinsic consensus.

Specifications for Each JSON Command:

### deployment

The sender deploys a new inscription following bnb-48 standard and acts as the role of owner.

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "deploy"|
|tick|string|yes|symbol of this inscription token|
|max|int|yes|max supply for this inscription token|
|lim|int|yes|max amount for each mint transaction|
|miners|array|no|array of miners consensus addresses. once set, mint is valid only if the tx is mined by one of miners listed here|

### recap

The sender, must be the owner, adjust the max supply of the inscription. 

New max supply must not be bigger than last valid one. In another word, it's only possible to shrink supply. Increasing is not allowed.

Right at the block height where recap command is confirmed on chain, if the total minted amount is less than or equal to the number in command, the number in command becomes the new valid max supply; Otherwise, the actual minted amount is the new valid max supply.

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "recap"|
|tick|string|yes|symbol of this inscription token|
|max|int|yes|new target max supply for this inscription token|
