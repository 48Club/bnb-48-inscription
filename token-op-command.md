# deployment

The sender deploys a new inscription following bnb-48 standard and acts as the role of owner.

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "deploy"|
|tick|string|yes|symbol of this inscription token|
|decimals|U256|optional|must not be less than 0, default 0, max 18. this parameter will be adopted by all parameters regarding balance or change of token amount, including `max` `lim` `amt` etc.|
|max|U256|yes|max supply for this inscription token, must be positive|
|lim|U256|yes|max amount for each mint transaction, must be positive, must be divisible by `max`|
|ratelim|U256|optiona|how many mint txs is allowed at the same block height from an identical sender|
|miners|array\[address\]|optional|array of miners consensus addresses. once set, mint is valid only if the tx is mined by one of miners listed here. If empty array is provided, it means no restrictions on miners.|
|minters|array\[address\]|optional|array of minters addresses. once set, only addresses in this array are eligible to send mint command (don't have to be the receiver)|
|reserves|map\{address:amount\}|optional|array of initial distribution on a series of addresses, sum
of amounts in which is part of max supply|
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
  "ratelim":"10",
  "miners":[
    "0x72b61c6014342d914470eC7aC2975bE345796c2b"
  ],
  "minters":[
    "0x72b61c6014342d914470eC7aC2975bE345796c2b"
  ],
  "reserves":[
    "0x72b61c6014342d914470eC7aC2975bE345796c2b":"100000000",
    "0x82b61c6014342d914470eC7aC2975bE345796c2b":"100000000"
  ],
  "commence":"300000000"
}
```
In this case: 
1. the max supply is `max` / 10^`decimal` = 3388230
2. the limit of each mint is `lmt` / 10^`decimal` = 1
3. Only the first 10 mints in each block height are valid; starting from the 11th mint, the indexer should consider it as invalid.
4. `mint` won't be valid until height 300000000
5. Two addresses received airdrop, 100.00 for each. The remaining mintable supply is
   3388030, from the very beginning.

Moreover, `tick-hash` is defied as deploy hash e.g. `tick-hash` of bnb-48 fans is `0xd893ca77b3122cb6c480da7f8a12cb82e19542076f5895f21446258dc473a7c2`

# recap

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

# mint

`from` address mint a deployed inscription for `to` address of the carrier tx, while `from` must not be an contract unless it's one of the `minters` parameter of corresponding deploy command.

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
# transfer

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
# burn

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

# approve

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
# transferFrom

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
