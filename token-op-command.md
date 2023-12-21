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

## Data format:
The data part typically consists of an serialized data object following [data URL scheme](https://datatracker.ietf.org/doc/html/rfc2397). If the format notation is not provided, data object will be treated as `application/json` defaultly.

On chains which don't support plain text memo, like EVM compatible chains, data load should be converted from utf-8 to hex before being put in to call data.

An example:
```
data:,
{
  "p":"bnb-48",
  "op":"command",
  "parameter1":"value"
  ...
}
```
or

```
data:application/json, 
{
  "p":"bnb-48",
  "op":"command",
  "parameter1":"value"
  ...
}
```
Where json is explicitly specified.

Indexer should correctly parse the data object according to data format instead of relying on a fixed piece of (hex) data, which means, spaces and breaks don't change a command, as well as the serialized sequence of key-value pairs. 

To maintain the compatibility of cross-platform, all tuples in data object should be a string, i.e. quoted by \".

Bulk commands are allowed when all commands share an op. If multiple commands is carried in a sigle transaction, the data should be packed in an array named `cmdq` i.e. command queue, each item of which contains a complete command, and each command will be handled in order. In which case, all combined commands are executed atomicly, i.e. either all of them are successfully executed or none of them.

Specially, following commands can not be part of a bulk commands tx. If bulk commands in a single transaction contains one of them, the entire bulk should be considered as invalid:
`deploy`
`mint`
`recap`


example:

```
data:application/json,
{
  "p":"bnb-48",
  "op":"bulk",
  "cmdq":[
    {
      "p":"bnb-48",
      "op":"transfer",
      "tick":"token1",
      ...
    },
    {
      "p":"bnb-48",
      "op":"transfer",
      "tick":"token2",
      ...
    }
  ]
}
```

## Command

### deployment

The sender deploys a new inscription following bnb-48 standard and acts as the role of owner.

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "deploy"|
|tick|string|yes|symbol of this inscription token|
|decimals|U256|optional|must not be less than 0, default 0, max 18. this parameter will be adopted by all parameters regarding balance or change of token amount, including `max` `lim` `amt` etc.|
|max|U256|yes|max supply for this inscription token, must be positive|
|lim|U256|yes|max amount for each mint transaction, must be positive, must be divisible by `max`|
|miners|array\[address\]|optional|array of miners consensus addresses. once set, mint is valid only if the tx is mined by one of miners listed here. Optional but must not be empty array.|
|minters|array\[address\]|optional|array of minters addresses. once set, mint is valid only if the `from` address is one of minters listed here. Optional but must not be empty array.|
|commence|U256|optiona|the earliest block height when mint of this token is valid|

application/json Example:
```
data:,
{
  "p":"bnb-48",
  "op":"deploy",
  "tick":"cz",
  "decimals":"6",
  "max":"3388230000000",
  "lim":"1000000",
  "miners":[
    "0x72b61c6014342d914470eC7aC2975bE345796c2b"
  ],
  "minters":[
    "0x72b61c6014342d914470eC7aC2975bE345796c2b"
  ],
  "commence":"300000000"
}
```
In this case: 
the max supply is `max` / 10^`decimal` = 3388230
the limit of each mint is `lmt` / 10^`decimal` = 1

The txhash of this very transaction which carries the deploy command is an important unique identity for this inscription token

Moreover, `tick-hash` is defied as deploy hash.

`tick-hash` of bnb-48 fans is `0xd893ca77b3122cb6c480da7f8a12cb82e19542076f5895f21446258dc473a7c2`

`mint` won't be valid until height 300000000

### recap

The sender, must be the owner (sender of the deploy op, and must not be reannounced), adjusts the parameter of an previously deployed inscription. 

New max supply must not be bigger than last valid one. In another word, it's only possible to shrink supply. Increasing is not allowed.

Right at the block height where recap command is confirmed on chain, if the total minted amount is less than or equal to the number in command, the number in command becomes the new valid max supply; Otherwise, the actual minted amount is the new valid max supply.

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "recap"|
|~tick~|string|deprecated|symbol of this inscription token|
|tick-hash|string|yes|id of this inscription token|
|max|U256|yes|new target max supply for this inscription token,must not be bigger than previous valid max supply|

application/json Example:
```
data:,
{
  "p":"bnb-48",
  "op":"recap",
  "tick-hash":"0xd893ca77b3122cb6c480da7f8a12cb82e19542076f5895f21446258dc473a7c2",
  "max":"1000000"
}
```

### mint

`from` address mint a deployed inscription for `to` address of the carrier tx, while `from` must not be an contract.Otherwise it will be invalid command anyway even if it's in the minters parameter of deploy command.

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "mint"|
|~tick~|string|deprecated|symbol of this inscription token|
|tick-hash|string|yes|id of this inscription token|
|amt|U256|yes|non-zero, must not be bigger than the lim parameter in deploy command|


application/json Example:
```
data:,
{
  "p":"bnb-48",
  "op":"mint",
  "tick-hash":"0xd893ca77b3122cb6c480da7f8a12cb82e19542076f5895f21446258dc473a7c2",
  "amt":"1"
}
```
### transfer

The sender, transfer its own inscription to other wallet.

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "transfer"|
|tick-hash|string|yes|id of this inscription token|
|to|address|yes|asset receiver|
|amt|U256|yes|non-zero, must not be bigger than balance of current sender address|


application/json Example:
```
data:,
{
  "p":"bnb-48",
  "op":"transfer",
  "tick-hash":"0xd893ca77b3122cb6c480da7f8a12cb82e19542076f5895f21446258dc473a7c2",
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
|tick-hash|string|yes|id of this inscription token|
|amt|U256|yes|non-zero, must not be bigger than balance of current sender address|


application/json Example:
```
data:,
{
  "p":"bnb-48",
  "op":"burn",
  "tick-hash":"0xd893ca77b3122cb6c480da7f8a12cb82e19542076f5895f21446258dc473a7c2",
  "amt":"1"
}
```

### approve

The sender sets the max number the spender wallet is approved to transfer on behalf of the sender

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "approve"|
|tick-hash|string|no|id of this inscription token|
|spender|address|yes|spender|
|amt|U256|yes|must not be bigger than the max supply|


application/json Example:
```
data:,
{
  "p":"bnb-48",
  "op":"approve",
  "tick-hash":"0xd893ca77b3122cb6c480da7f8a12cb82e19542076f5895f21446258dc473a7c2",
  "spender":"0x72b61c6014342d914470eC7aC2975bE345796c2b",
  "amt":"1"
}
```
### transferFrom

The sender, transfer inscription of the parameter `from`  to the transaction `to` address.
Once succeed, transfered amount should be deducted from sender's approved amount by parameter from.

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "transferFrom"|
|tick-hash|string|no|id of this inscription token|
|from|address|yes|of which the sender spend on behalf|
|to|address|yes|asset receiver|
|amt|U256|yes|must not be bigger than balance of parameter from address, must not be bigger than sender's remaining approved amount by parameter from|


application/json Example:
```
data:,
{
  "p":"bnb-48",
  "op":"transferFrom",
  "tick-hash":"0xd893ca77b3122cb6c480da7f8a12cb82e19542076f5895f21446258dc473a7c2",
  "from":"0x72b61c6014342d914470eC7aC2975bE345796c2b",
  "to":"0x72b61c6014342d914470eC7aC2975bE345796c2b",
  "amt":"1"
}

```
