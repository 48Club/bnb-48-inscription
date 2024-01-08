# create artset
Author inscript  multi-media on chain, preparing for future release event.
`as-hash` is defined as the hash of the transaction carries this op
owner of this artSet will automatically be the sender
each media (or art) should be allocated to an unique id, U256, starts from 1

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "artset"|
|as-tick|string|optional|symbol of this artset|
|medias|array[string]|yes|media array of this collection, content|

# append artset
artset owner append some more arts into existing artset
all arts must not share media id i.e. any media id must be unique
so id of arts starts from the existing id + 1

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "appendartset"|
|as-hash|string|optional|existing|
|medias|array[string]|yes|media array of this collection, content|

# release collection

Release a NFT collection from one or more art sets
`collection-hash` is defined as the hash of the transaction carries this op

owner of this collection will automatically be the sender

once released, the artset will be frozen i.e. can not be appended any more

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "releasecol"|
|title|string|yes|Title of this collection|
|desc|string|yes|Description of this collection|
|as-hash|as-hash|yes|artset to be included in this collection; artSet must be owned by the `from` address; |
|whitelists|array[address]|optional|who can forge nft in this collection regardless of pow setting; if not provided or empty array is provided, all addresses are treated as whitelisted|
|pow|U256|optional|default 0; notice this value means nothing to whitelisted addresses. Generally: Only when the trailing digits of the XOR result between forge txhash and the block height (in hexadecimal) are `pow` number of zeros, is it considered success.|
|reforgetoken|tick-hash|optional|inscription token as fragments of this collection; if not provided, tick-hash will be set to $fans ' tick-hash i.e. 0xd893ca77b3122cb6c480da7f8a12cb82e19542076f5895f21446258dc473a7c2 |
|reforgeprice|U256|optional|How many fragments can one NFT be melt into, or reforged with; this value should be the original number without decimals consideration.|
|taxrate|U256|optional|decimal 4, round to floor; e.g. 500 means 0.05 i.e. 5% ; this parameter means the tax rate paid to collection owner when an NFT is traded on market; market dApp should respect this parameter, but this is not guaranteed by code|


# update collection

update a released collection. The new whitelist set will replace the old one entirely.
must be sent from the owner address

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "updatecol"|
|collection-hash|string|yes|collection-hash of the collection|
|title|string|optional|Title of this collection|
|desc|string|optional|Description of this collection|
|whitelists|array[address]|optional|who can mint nft free in this collection|
|owner|address|optional|current owner of this collection|
|taxrate|U256|optional|decimal4, round to floor|


# forge a NFT

Pick an available art from collection and mint it into an NFT as a whitelisted wallet.
If no whitelisted provided for this collection, all wallets are treated as whitelisted.
If `from` wallet is not whitelisted, this operation fails.

forge operation can not be part of bulk operations.

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "forge"|
|collection-hash|string|yes|of the NFT set|
|artid|U256|yes|id of the art in the entire artset(s); notice that once an artid is (re)forged, it can not be forged again until it is melted|
|to|U256|address|target address|

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
|to|U256|address|target address|


# melt a NFT

Melt an self-held NFT into fragments inscription tokens, fund comes from collection deposit account
Notice that a NFT is only meltable when collection deposit account is filled by any previous reforge operation
Once done, corresponding token deducted from collection deposit account
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

