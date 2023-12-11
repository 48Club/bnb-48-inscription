# Standards of bnb-48 Inscription

## Introduction:
The process of inscription involves a sequence of commands affixed to transactions in hexadecimal format. 

The bnb-48 inscription standard ensures a structured and identifiable format for commands attached to transactions on the BNB Smart Chain. The integration of these specifications aims to provide clarity and coherence in the execution of operations.


## Main Features
1. Block Chain Transparency. bnb-48 standard is suitable for any blockchain, as far as transactions on this chain supports calldata/memo/remark etc. It's super easy for indexers to track bnb-48 inscription even across different blockchains.
2. Unlike an NFT standard, bnb-48 is not designed for non-fungible tokens. This design choice facilitates native bulk transfers.
3. Providing functionality interfaces similar to ERC-20, these interfaces form the foundational infrastructure for DeFi.
4. Multi commands are allowed to be carried in a single tx, ensuring a cost-effective and efficient process.
5. Multiple serialization formats support. Developer is free to choose read-freindly format like json or efficiency orientation format like protobuffer.
6. Backward compatible upgradability.


Notably, bnb-48 serves as a standard for inscription originated on the BNB Smart Chain, with '48' denoting its association with the 48 Club.

## Data format:
The data part typically consists of an serialized data object following a format notation (ALL CAPITAL). If the format notation is not provided, data object will be treated as JSON defaultly.

On chains which don't support plain text memo, like EVM compatible chains, data load should be converted from utf-8 to hex before being put in to call data.

An example:
```
data:,
{
  "p":"bnb-48",
  "op":"command"
}
```
or

```
data:JSON, 
{
  "p":"bnb-48",
  "op":"command"
}
```
Where `JSON` is explicitly specified.

Bulk commands are allowed. If multiple commands is carried in a sigle transaction, the data should be packed in an array, each item of which contains a complete command, and each command will be handled in order. In which case, all combined commands are executed atomicly, i.e. either all of them are successfully executed or none of them.

example:

```
data:JSON,
[
  {
    "p":"bnb-48",
    "op":"deploy",
    "tick":"token1"
  },
  {
    "p":"bnb-48",
    "op":"deploy",
    "tick":"token2"
  }
]
```
Specially, `mint` command must be a standalone command. If bulk commands in a single transaction contains a `mint`, the entire transaction should be considered as invalid.


Indexer should correctly parse the data object according to data format instead of relying on a fixed piece of (hex) data.

## Command

### deployment

The sender deploys a new inscription following bnb-48 standard and acts as the role of owner.
The tick value must be unique, the second deploy of the same tick should be ignored by indexer.

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "deploy"|
|tick|string|yes|symbol of this inscription token|
|decimal|U256|optional|must not be less than 0, default 0, max 18. this parameter will be adopted by all parameters regarding balance or change of token amount, including `max` `lim` `amt` etc.|
|max|U256|yes|max supply for this inscription token, must be positive|
|lim|U256|yes|max amount for each mint transaction, must be positive, must be divisible by `max`|
|miners|array\[address\]|no|array of miners consensus addresses. once set, mint is valid only if the tx is mined by one of miners listed here. If empty array is provided, it means no restrictions on miners.|

JSON Example:
```
data:,
{
  "p":"bnb-48",
  "op":"deploy",
  "tick":"fans",
  "decimal":"6",
  "max":"3388230000000",
  "lim":"1000000",
  "miners":[
    "0x72b61c6014342d914470eC7aC2975bE345796c2b"
  ]
}
```
In this case: 
the max supply is `max` / 10^`decimal` = 3388230
the limit of each mint is `lmt` / 10^`decimal` = 1

### recap

The sender, must be the owner, adjust the max supply of an previously deployed inscription. 

New max supply must not be bigger than last valid one. In another word, it's only possible to shrink supply. Increasing is not allowed.

Right at the block height where recap command is confirmed on chain, if the total minted amount is less than or equal to the number in command, the number in command becomes the new valid max supply; Otherwise, the actual minted amount is the new valid max supply.

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "recap"|
|tick|string|yes|symbol of this inscription token|
|max|U256|yes|new target max supply for this inscription token,must be positive, must not be bigger than previous valid max supply|


JSON Example:
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

Sender mint a deployed inscription for `to` address of the carrier tx

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "mint"|
|tick|string|yes|symbol of this inscription token|
|amt|U256|yes|must be positive, must not be bigger than the lim parameter in deploy command|


JSON Example:
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

The sender, transfer its own inscription to other wallet.

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "transfer"|
|tick|string|yes|symbol of this inscription token|
|to|address|yes|asset receiver|
|amt|U256|yes|must be positive, must not be bigger than balance of current sender address|


JSON Example:
```
data:,
{
  "p":"bnb-48",
  "op":"transfer",
  "tick":"fans",
  "to":"0x72b61c6014342d914470eC7aC2975bE345796c2b",
  "amt":"1"
}
```
### burn

The sender, burn its own inscription

Total supply of this inscription token should be deducted accordingly

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "burn"|
|tick|string|yes|symbol of this inscription token|
|amt|U256|yes|must be positive, must not be bigger than balance of current sender address|


JSON Example:
```
data:,
{
  "p":"bnb-48",
  "op":"burn",
  "tick":"fans",
  "amt":"1"
}
```

### approve

The sender sets the max number the spender wallet is approved to transfer on behalf of the sender

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "approve"|
|tick|string|yes|symbol of this inscription token|
|spender|address|yes|spender|
|amt|U256|yes|must be positive, must not be bigger than the max supply|


JSON Example:
```
data:,
{
  "p":"bnb-48",
  "op":"approve",
  "tick":"fans",
  "spender":"0x72b61c6014342d914470eC7aC2975bE345796c2b",
  "amt":"1"
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
|to|address|yes|asset receiver|
|amt|U256|yes|must be positive, must not be bigger than balance of parameter from address, must not be bigger than sender's remaining approved amount by parameter from|


JSON Example:
```
data:,
{
  "p":"bnb-48",
  "op":"transferFrom",
  "tick":"fans",
  "from":"0x72b61c6014342d914470eC7aC2975bE345796c2b",
  "to":"0x72b61c6014342d914470eC7aC2975bE345796c2b",
  "amt":"1"
}
```
