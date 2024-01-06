# upload artset
Author inscript  multi-media on chain, preparing for future release event.
upload artset can not be part of bulk operations
`as-hash` is defined as the hash of the transaction carries this op
owner of this artSet will automatically be the sender

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "artset"|
|as-tick|string|optional|symbol of this artset|
|medias|map{U256:string}|yes|media map of this collection, id:content|
|format|string|yes|the format of media content how frontend parses|

# release collection

Release a NFT collection from one or more art sets
`collection-hash` is defined as the hash of the transaction carries this op

owner of this collection will automatically be the sender

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "releasecol"|
|artsets|array[as-hash]|yes|artSets to be included in this collection; all artSets must be owned by the `from` address; each artSet can only be included in one collection;all artSets must not share media id i.e. any media id must be unique across all these artSets|
|whitelists|array[address]|optional|who can mint nft free in this collection; if not provided or empty array is provided, there is no restriction on forging|
|tick-hash|string|optional|inscription token as price/fragments of this collection; if not provided, tick-hash will be set to $fans ' tick-hash i.e. 0xd893ca77b3122cb6c480da7f8a12cb82e19542076f5895f21446258dc473a7c2 |
|forgeprice|U256|optional|default 0; How many token the whitelist pays to owner while forging; this value should be the original number without decimals consideration.|
|reforgeprice|U256|optional|How many fragments can one NFT be melt into, or forged with; this value should be the original number without decimals consideration.|
|taxrate|U256|optional|decimal 4, round to floor; e.g. 500 means 0.05 i.e. 5% ; this parameter means the tax rate paid to collection owner when an NFT is traded on market; market dApp should respect this parameter, but this is not guaranteed by code|

# update collection

update owner/whitelist/tax of a released collection. The new whitelist set will replace the old one entirely.
must be sent from the owner address

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "updatecol"|
|collection-hash|string|yes|collection-hash of the collection|
|whitelists|array[address]|optional|who can mint nft free in this collection|
|owner|address|optional|current owner of this collection|
|forgeprice|U256|optional|default 0; How many token the whitelist pays to owner while forging; this value should be the original number without decimals consideration.|
|taxrate|U256|optional|decimal4, round to floor|


# forge a NFT

Pick an available art from collection and mint it into an NFT as a whitelisted wallet
If `from` wallet is not whitelisted, this operation fails.
If there is a non-zero forge price, it should be deducted from sender's wallet, otherwise fails.
Notice that this price directly goes to owner, and not refundable.

Each whitelist is entitled to forge one NFT in this collection lifetime, the second attempt will fail. Indexer should keep an forge history to make sure this rule works.

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "forge"|
|collection-hash|string|yes|of the NFT set|
|artid|U256|yes|id of the art in the entire artset(s); notice that once an artid is (re)forged, it can not be forged again until it is melted|

# reforge a NFT

Pick an available art from collection and mint it into an NFT with specified amount of fragments (inscription token) 
Corresponding price will be deducted from `from` wallet, if there is no sufficient holding, this operation fails.
Fragments inscription token will be deposit to a special account associated with this nft collection, called collection deposit account.

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "reforge"|
|collection-hash|string|yes|of the NFT set|
|artid|U256|yes|id of the art in the entire artset(s); notice that once an artid is (re)forged, it can not be forged again until it is melted|


# melt a NFT

Melt an self-held NFT into fragments inscription tokens, fund comes from collection deposit account
Notice that a NFT is only meltable when collection deposit account is filled by any previous reforge operation
If there is no enough token, melt fails

Once melt, art id of this NFT becomes available again, can be (re)forged again.

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "melt"|
|collection-hash|string|yes|of the NFT set|
|artid|U256|yes|id of the art of the NFT|

# pass a NFT

Transfer an self-held NFT to another address

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "pass"|
|collection-hash|string|yes|of the NFT set|
|artid|U256|yes|id of the art of the NFT|
|to|U256|address|target address|

