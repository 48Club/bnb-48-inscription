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
The spaces, breaks and items order in serialized json don't matter, which means the following is equally valid:

```
data:
,
{
"op":opvalue,
"p"
:
  "bnb-48"
 ... "tuple":tuplevalue
}
```

Indexer should correctly parse the json part instead of relying on a fixed piece of hex data.

When asset movement is involved, the asset receiver will always be the receiver (to address) of the carrier transaction. This rule makes sure bnb-48 standard is secured by the blockchain intrinsic consensus.

Notice that when approved, sender is possible to move inscription on behalf of other addresses.

Specifications for Each JSON Command:

### deployment

The sender deploys a new inscription following bnb-48 standard and acts as the role of owner.
The tick value must be unique, the second deploy of the same tick should be ignored by indexer.

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "deploy"|
|tick|string|yes|symbol of this inscription token|
|max|int|yes|max supply for this inscription token, must be positive|
|lim|int|yes|max amount for each mint transaction, must be positive, must be divisible by `max`|
|miners|array\[address\]|no|array of miners consensus addresses. once set, mint is valid only if the tx is mined by one of miners listed here. If empty array is provided, it means no restrictions on miners.|

Example:
```
data:,
{
  "p":"bnb-48",
  "op":"deploy",
  "tick":"fans",
  "max":"3388230",
  "lim":"1",
  "miners":[
    "0x72b61c6014342d914470eC7aC2975bE345796c2b"
  ]
}
```

### recap

The sender, must be the owner, adjust the max supply of an previously deployed inscription. 

New max supply must not be bigger than last valid one. In another word, it's only possible to shrink supply. Increasing is not allowed.

Right at the block height where recap command is confirmed on chain, if the total minted amount is less than or equal to the number in command, the number in command becomes the new valid max supply; Otherwise, the actual minted amount is the new valid max supply.

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "recap"|
|tick|string|yes|symbol of this inscription token|
|max|int|yes|new target max supply for this inscription token,must be positive, must not be bigger than previous valid max supply|


Example:
```
data:,
{
  "p":"bnb-48",
  "op":"recap",
  "tick":"fans",
  "max":"1000000"
}
```
### mint

Sender mint a deployed inscription for `to` address

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "mint"|
|tick|string|yes|symbol of this inscription token|
|amt|int|yes|must be positive, must not be bigger than the lim parameter in deploy command|


Example:
```
data:,
{
  "p":"bnb-48",
  "op":"mint",
  "tick":"fans",
  "amt":"1"
}
```
### transfer

The sender, transfer its own inscription to the `to` wallet address.

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "transfer"|
|tick|string|yes|symbol of this inscription token|
|amt|int|yes|must be positive, must not be bigger than balance of current sender address|


Example:
```
data:,
{
  "p":"bnb-48",
  "op":"transfer",
  "tick":"fans",
  "amt":"1"
}
```

### approve

The sender sets the max number the `to` wallet is approved to transfer on behalf of the sender

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "approve"|
|tick|string|yes|symbol of this inscription token|
|amt|int|yes|must be positive, must not be bigger than the max supply|
|spender|address|yes|spender|

Example:
```
data:,
{
  "p":"bnb-48",
  "op":"approve",
  "tick":"fans",
  "amt":"1",
  "spender":"0x72b61c6014342d914470eC7aC2975bE345796c2b"
}
```
### transferFrom

The sender, transfer inscription of the parameter from  to the transaction `to` address.
Once succeed, transfered amount should be deducted from sender's approved amount by parameter from.

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "transferFrom"|
|tick|string|yes|symbol of this inscription token|
|from|address|yes|of which the sender spend on behalf|
|amt|int|yes|must be positive, must not be bigger than balance of parameter from address, must not be bigger than sender's remaining approved amount by parameter from|


Example:
```
data:,
{
  "p":"bnb-48",
  "op":"transferFrom",
  "tick":"fans",
  "from":"0x72b61c6014342d914470eC7aC2975bE345796c2b",
  "amt":"1"
}
```
