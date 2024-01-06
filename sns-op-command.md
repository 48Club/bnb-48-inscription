# SNS Extension of bnb-48 Inscription

## Introduction:

This document states entension of bnb-48 inscription regarding social network.

Inscription is a perfect solution for social network in aspects of light-storage, as well as blockchain-finality at the same time.

## Main Features

1. Public verifiable. Use blockchain transactions as storage.
2. Lower gas. It's the feature of inscription.
3. Native DID. Decentralized IDentity.

## Command

### declare

The sender declare to be a post author, or reconfig parameters

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "sns"|
|cmd|string|yes|fixed, "declare"|

### resign

The sender resign to quit being a post author, can not post or receive dm since

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "sns"|
|cmd|string|yes|fixed, "resign"|

### post

The sender send a public post

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "sns"|
|cmd|string|yes|fixed, "post"|

### direct message

The sender send a text message

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "sns"|
|cmd|string|yes|fixed, "dm"|
|target|address|yes|to address|
|content|string|yes|content of this msg|

### follow

The sender follow posts of a specified target address

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "sns"|
|cmd|string|yes|fixed, "follow"|
|target|address|yes|target address|

### unfollow

The sender follow posts of a specified target address

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "sns"|
|cmd|string|yes|fixed, "unfollow"|
|target|address|yes|target address|

### allow dm

The sender allow dms from target

|tuple|type|mandatory|description|
|-|-|-|-|
|p|string|yes|fixed, "bnb-48"|
|op|string|yes|fixed, "sns"|
|cmd|string|yes|fixed, "allowdm"|
|target|address|yes|to address|
