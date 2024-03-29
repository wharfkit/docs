---
title: ABI
description: A data type representing an application binary interface (ABI) which can be used to describe a smart contract and provide instructions for data serialization.
category: Antelope
published: true
---

# ABI

The ABI ([Application Binary Interface](https://en.wikipedia.org/wiki/Application_binary_interface)) in Antelope-based blockchains is a JSON data structure that defines the structure of a [Smart Contract](#) and instructs the [Serializer](#) on how to encode or decode data.

## Classes


## Loading an ABI

An `ABI` can be loaded from a public API using the `APIClient`.

```ts
const client = new APIClient({ url: "https://jungle4.greymass.com" })

const response = await client.v1.chain.get_abi("eosio")

if (response.abi) {
  const abi = ABI.from(response.abi)
  assert.equal(abi instanceof ABI, true)
}
```

```ts
const abi: ABIDef = {
    structs: [
        {
            base: '',
            name: 'transfer',
            fields: [
                {name: 'from', type: 'name'},
                {name: 'to', type: 'name'},
                {name: 'quantity', type: 'asset'},
                {name: 'memo', type: 'string'},
            ],
        },
    ],
    actions: [{name: 'transfer', type: 'transfer', ricardian_contract: ''}],
}
```

```ts
const abi: ABIDef = '{"structs":[{"base":"","name":"transfer","fields":[{"name":"from","type":"name"},{"name":"to","type":"name"},{"name":"quantity","type":"asset"},{"name":"memo","type":"string"}]}],"actions":[{"name":"transfer","type":"transfer","ricardian_contract":""}]}'
```

An `ABI` can also be defined manually as code:

```ts
const abi = ABI.from({
  version: "eosio::abi/1.0",
  types: [],
  variants: [],
  structs: [],
  actions: [],
  tables: [],
  ricardian_clauses: [],
})
```

## Using

### When encoding an `Action`

```ts
const { abi } = await client.v1.chain.get_abi("eosio.token")

const data = {
  authorization: [
    {
      actor: "corecorecore",
      permission: "active",
    },
  ],
  account: "eosio.token",
  name: "transfer",
  data: {
    from: "corecorecore",
    to: "teamgreymass",
    quantity: "0.0042 EOS",
    memo: "eosio-core is the best <3",
  },
}

const action = Action.from(data, abi)

/**
{
    account: 'eosio.token',
    name: 'transfer',
    authorization: [ { actor: 'corecorecore', permission: 'active' } ],
    data: 'a02e45ea52a42e4580b1915e5d268dca2a0000000000000004454f530000000019656f73696f2d636f7265206973207468652062657374203c33'
}
*/
```

### When decoding an `Action`

```ts
const { abi } = await client.v1.chain.get_abi("eosio.token")

const data = {
  account: "eosio.token",
  name: "transfer",
  authorization: [{ actor: "corecorecore", permission: "active" }],
  data: "a02e45ea52a42e4580b1915e5d268dca2a0000000000000004454f530000000019656f73696f2d636f7265206973207468652062657374203c33",
}

const action = Action.from(data, abi)

const decoded = action.decodeData(abi)

/**
{
    from: Name { value: UInt64 { value: [BN] } },
    to: Name { value: UInt64 { value: [BN] } },
    quantity: Asset {
        units: Int64 { value: [BN] },
        symbol: Symbol { value: [UInt64] }
    },
    memo: 'eosio-core is the best <3'
}
*/
```

### When decoding `Action` data using the Serializer

```ts
const { abi } = await client.v1.chain.get_abi("eosio.token")

const data =
  "a02e45ea52a42e4580b1915e5d268dca2a0000000000000004454f530000000019656f73696f2d636f7265206973207468652062657374203c33"

const decoded = Serializer.decode({ data, abi, type: "transfer" })

/**
{
    from: Name { value: UInt64 { value: [BN] } },
    to: Name { value: UInt64 { value: [BN] } },
    quantity: Asset {
        units: Int64 { value: [BN] },
        symbol: Symbol { value: [UInt64] }
    },
    memo: 'eosio-core is the best <3'
}
*/
```
