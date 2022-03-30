---
title: PulseX DEX
---

PulseX is a [Uniswap V2](https://docs.uniswap.org/protocol/V2/introduction) compatible DEX which was launced for the first time with PulseChain Testnet v2b.

# Testnet v2b
Smart contract of PulseXRouter03: `0xb4A7633D8932de086c9264D5eb39a8399d7C0E3A`

Smart contract of Wrapped PLS: `0x8a810ea8B121d08342E9e7696f4a9915cBE494B7`

## GraphQL
PulseX provide GraphQL [endpoint](https://graph.v2b.testnet.pulsechain.com/subgraphs/name/pulsechain/pulsex).

Example query to list all token pairs:
```
{
  pairs {
    id,
    name,
    totalTransactions
  }
}
```

Example query to list details of a specified token pair:
```
{
  pair(id: "0x09ca062dff7272ddd97379bd225239de2834719f") {
     token0 {
       id
       symbol
       name
       derivedPLS
     }
     token1 {
       id
       symbol
       name
       derivedPLS
     }
     reserve0
     reserve1
     reserveUSD
     trackedReservePLS
     token0Price
     token1Price
     volumeUSD
 }
}
```
