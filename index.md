---
title: Unofficial PulseChain Documentation
---

This page contains technical and developer documentation about [PulseChain](https://www.pulsechain.com/) blockchain network and some of the projects running on PulseChain.
This documentation is created by community independently on the PulseChain development team with the intent to simplify onboarding of new developers and blockchain projects and to support the development community. You can contribute to this documentation through [PulseChain Docs Github Repository](https://github.com/hexikani/pulsechain-docs).

PulseChain is a full state for of Ethereum 2.0 with 10 seconds block time. Native token of PulseChain is _Pulse_ (PLS). Smallest unit of PLS used to referred as _Beat_ (PLS to Beat is the same as ETH to Wei).

PulseChain currently runs only as a Testnet version *V3*. There is no Mainnet yet.
{:.warning}

If you are interested in development for PulseChain you can contact developers in the [PulseDEV Telegram group](https://t.me/PulseDEV).
{:.success}

# Consensus, Validators and Fees

PulseChain uses same consensus as Ethereum 2.0 and implements EIP-1159.

# Validators & Staking

Validators and stakes from are managed through a set of smart contracts. PulseChain's default user interface for this is dApp which is dedicated for each version of the PulseChain:
- [Validators management for Testnet V3](https://launchpad.v3.testnet.pulsechain.com/)

# Testnets

Official documentation for the PulseChain Testnet is available [in the Gitlab repository](https://gitlab.com/pulsechaincom/pulsechain-testnet).
{:.info}
Testnet uses `tPLS` as the currency symbol for the native coin.

## Testnet v1

Testnet v1 was switched off, was replaced with v2.
{:.warning}

- Last Ethereum pre-fork block `13224745`
- Chain ID: `940`
- Public RPC endpoint: `https://rpc.testnet.pulsechain.com`
- Public WebSocket endpoint: `wss://ws.testnet.pulsechain.com`

## Testnet v2

Testnet v2 was switched off, was replaced with v2b.
{:.warning}

Basic information:
- Last Ethereum pre-fork block `13224745` (same as v1)
- Chain ID: `940`
- Public RPC endpoint: `https://rpc.v2.testnet.pulsechain.com`
- Public WebSocket endpoint: `ws://ws.v2.testnet.pulsechain.com`

## Testnet v2b

Testnet v2b added:
- PLS and PLSX balances
- PulseX DEX

Basic information:
- Block time: 3 seconds
- Block gas limit: 30,000,000
- Last Ethereum pre-fork block: `14360999`
- Chain ID: `941`
- Public RPC endpoint: `https://rpc.v2b.testnet.pulsechain.com`
- Public WebSocket endpoint: `wss://ws.v2b.testnet.pulsechain.com`

## Testnet V3

Consensus was replaced with native Ethereum 2.0 one.

Basic information:
- Block time: 10 seconds
- Block gas limit: 30,000,000
- Faucet to get 10 tPLS: https://faucet.v3.testnet.pulsechain.com/
- PulseChain Block Explorer: https://scan.v3.testnet.pulsechain.com/
- PulseChain Beacon Explorer: https://beacon.v3.testnet.pulsechain.com/
- Last Ethereum pre-fork block: `16492699`
- Chain ID: `942`
- Public RPC endpoint: `https://rpc.v3.testnet.pulsechain.com`
- Public WebSocket endpoint: `wss://ws.v3.testnet.pulsechain.com`
- Canonical staking deposit contract address `0x3693693693693693693693693693693693693693`
