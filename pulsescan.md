---
title: PulseScan
---

PulseScan is a fork of [BlockScout](https://github.com/blockscout/blockscout) blockchain explorer for PulseChain. Source code is available on [https://gitlab.com/pulsechaincom/blockscout](https://gitlab.com/pulsechaincom/blockscout).

It can be accessed at:
- For PulseChain Mainnet: [https://scan.pulsechain.com/](https://scan.pulsechain.com/)
- For PulseChain Testnet v4: [https://scan.v4.testnet.pulsechain.com/](https://scan.v4.testnet.pulsechain.com/)

Comparing to Etherscan, the PulseScan has limited capabilities, **but it's the best what we have at the moment**.

Known issues are:
- Blockscout keeps indexing nearly forever, on the Testnet there is a message like `X Blocks Indexed - We're indexing this chain right now. Some of the counts may be inaccurate.`
- There is visible delay in indexing of blocks visible as `Block Signed, processing...` message.
- Blockscout may not show information before the PulseChain fork consistently.

