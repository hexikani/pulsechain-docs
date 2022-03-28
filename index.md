---
title: PulseChain documentation
---

This page contains technical documentation about [PulseChain](https://www.pulsechain.com/) blockchain network and some of the projects running on PulseChain.
This documentation is created by community independently on the PulseChain development team with the intent to simplify onboarding of new developers and blockchain projects and to support the development community.

PulseChain is a full state for of Ethereum. PulseChain consensus was originally forked from older version of BSC and has been modified substantially. Later on many upstream Go-Ethereum changes were brought in. Code was also modified to support the dual consensus, because PulseChain has to support crossing the fork boundary and convert from post-London Ethereum's PoW to PulseChain's PoS. PulseChain has it own token burn (25% of transaction costs are burnt) which is unrelated to London EIP-1559. After all this the PulseChain code is closer to Go-Ethereum than to BSC.

Native token of PulseChain is _Pulse_ (PLS). Smallest unit of PLS is _Beat_ (PLS to Beat is the same as ETH to Wei).

PulseChain currently runs only as a Testnet version *v2b*. There is not Mainnet yet. Older Testnet v2 is still available too.
{:.warning}

If you are interested in development for PulseChain you can contact developers in the [PulseDEV Telegram group](https://t.me/PulseDEV).
{:.success}

# Consensus, Validators and Fees

PulseChain uses PoS _Proof-of-Stake_ (or more precisely _Proof-of-Staked-Authority_) consensus model with 3 seconds block time and low transaction fees. In this model there are _validator nodes_ which continuously pass in cycle the role of signing any new block on the chain. The set of active validator nodes can change after every 28800 blocks (approximately 24 hours) - this cycle is called _rotation_. Out of all registered validator nodes a maximum of 33 validators can be in rotation at any given time. 

This revenue is shared between all validators <!-- TODO: when this happens? --> proportionally to the amount of PLS staked with the address of the validator. Any user of PulseChain can stake their PLS with the incentive of receiving a share of revenue from the validator's the user staked to.

There is no limit to the number of registered validators, but at the time of registration:
- Non-refundable deposit is required to allow the validator to be in rotation.
- Validators determine what their revenue share with stakers will be in simple percentage 0%-100%.

At time of rotation:
- Validators already in rotation are paid fees they've accrued over the last cycle through the staking contract, which also distributes payments to the stakers automatically.
- Pending stakes become real stakes.
- Validators are then ranked by their total stake and the top 33 validators are selected for rotation.

Validators that are unresponsive or otherwise fail to produce their block in-turn will be _slashed_:
- Number of slashes is tracked over time.
- Once 50 slashes have been reached, the validator is given a _Misdemeanor_ - this zeros out the accrued fees the validator has earned up to that point in the rotation, but they can still earn fees for future blocks. The income stripped from the validator will later be distributed evenly among all validators. <!-- TODO: really all or top 33? -->
- Once 150 slashes have been reached, the validator is given a _Felony_ - this does everything the Misdemeanor and **removes the validator from the current rotation**.
- Slashes decay over time. Every rotation will remove up to 37 slashes from each validator. <!-- TODO: is Misdemeanor or Felony every decreased? -->
- Validators can unregister themselves if they wish to go offline.

# Validators & Staking dApp

Validators and stakes from are managed through a set of smart contracts. PulseChain's default user interface for this is dApp which is dedicated for each version of the PulseChain:
- [Validators & Staking for Testnet v2](https://stake.v2.testnet.pulsechain.com/)
- [Validators & Staking for Testnet v2b](https://stake.v2b.testnet.pulsechain.com/)

The HowToPulse.com team created [alternative dApp]() which can be used as well.

# Special PulseChain addresses
- `0x0000000000000000000000000000000000001000` - validator registration contract.
- `0x0000000000000000000000000000000000001001` - validator slashing contract.
- `0x0000000000000000000000000000000000001002` - staking contract.
- `0x0000000000000000000000000000000000000666` - burn address - 25% of each transaction's fees are sent here and burned.

# Testnets

Official documentation for the PulseChain Testnet is available [in the Gitlab repository](https://gitlab.com/pulsechaincom/pulsechain-testnet).
Testnet use `tPLS` ad the currency symbol for the native coin.

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
- Last Ethereum pre-fork block: `14360999`
- Chain ID: `941`
- Public RPC endpoint: `https://rpc.v2b.testnet.pulsechain.com`
- Public WebSocket endpoint: `wss://ws.v2b.testnet.pulsechain.com`

