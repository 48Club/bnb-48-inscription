# Standards of bnb-48 Inscription

## Introduction:
The process of inscription involves a sequence of commands affixed to transactions in hexadecimal format. Notably, bnb-48 serves as a standard for inscription on the BNB Smart Chain, with '48' denoting its association with the 48 Club.

The bnb-48 inscription standard ensures a structured and identifiable format for commands attached to transactions on the BNB Smart Chain. The integration of these specifications aims to provide clarity and coherence in the execution of operations.

## Format:
The call data format typically consists of an unmarched JSON following the predetermined string data:,

```
data:,{"p":"bnb-48","op":opvalue ... "tuple":tuplevalue}
```
Specifications for Each JSON Command:

### deployment

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "deploy"|
|tick|string|yes|symbol of this inscription token|
|max|int|yes|max supply for this inscription token|
|lim|int|yes|max amount for each mint|
|miners|array|no|array of miners consensus addresses. once set, mint is valid only if the tx is mined by one of miners listed here|

