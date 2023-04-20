---
title: PulseX DEX
---

PulseX is a [Uniswap V2](https://docs.uniswap.org/protocol/V2/introduction) compatible DEX, a PancakeSwap fork, which was launched for the first time with PulseChain Testnet v2b.

# Testnet v4

Smart contracts:
- Wrapped PLS: `0x70499adEBB11Efd915E3b69E700c331778628707`
- PulseX Token: `0x8a810ea8B121d08342E9e7696f4a9915cBE494B7`
- PulseXRouter02: `0xDaE9dd3d1A52CfCe9d5F2fAC7fDe164D500E50f7`
- PulseXFactory02: `0xFf0538782D122d3112F75dc7121F61562261c0f7`
- MasterChef: `0x3b34Fff74497cA21771ce9a0c11cb07490686A58`

# Testnet v3

Smart contracts:
- Wrapped PLS: `0x70499adEBB11Efd915E3b69E700c331778628707`
- PulseX Token: `0x3b34Fff74497cA21771ce9a0c11cb07490686A58`
- PulseXRouter02: `0xDaE9dd3d1A52CfCe9d5F2fAC7fDe164D500E50f7`
- PulseXFactory02: `0x6eFAfcb715F385c71d8AF763E8478FeEA6faDF63`
- MasterChef: `0x959a437F1444DaDaC8aF997E71EAF0479c810267`

# Testnet v2b

Smart contracts:
- Wrapped PLS: `0x8a810ea8B121d08342E9e7696f4a9915cBE494B7`
- PulseXRouter03: `0xb4A7633D8932de086c9264D5eb39a8399d7C0E3A`
- PulseXFactory02: `0xb242aA8A863CfcE9fcBa2b9a6B00b4cd62343f27`
- MasterChef: `0xB635be96898552bBe80043239c57ea864223fdC1`

## GraphQL
PulseX provide GraphQL [endpoint](https://graph.v4.testnet.pulsechain.com/subgraphs/name/pulsechain/pulsex).

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
