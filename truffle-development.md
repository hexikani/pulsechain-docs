---
title: Smart Contract Development
---

# Setup Truffle project

Create a project directory and initialise the NPM project and install Truffle:
```sh
npm init
```
and fill in properties for you project

Now install Truffle:
```
npm install -g --save-dev truffle
```

You can also install `truffle` locally (without `-g`), but then you have to use something like (for Linux):  
`alias truffle=./node_modules/.bin/truffle`
{:.info}

Now you can setup Truffle project:
```sh
truffle init
```

You can start your development by unboxing some of the [examples](https://trufflesuite.com/boxes/) e.g.:
```sh
truffle unbox tutorialtoken
```

or
```
truffle create contract YourContractName
truffle create test YourTestName
```

# Configure Truffle for Testnet

To connect to the public RPC node we need 2 modules:
```
npm install --save-dev dotenv @truffle/hdwallet-provider
```

Now edit `truffle-config.js` to include:
- the following 2 lines at the global scope:
```javascript
require('dotenv').config(); // read .env file and update process.env if present
const HDWalletProvider = require('@truffle/hdwallet-provider');
```
- and add the following key under the `network: {` key :
```javascript
    testnet: {
      provider: () =>
        new HDWalletProvider(
          (process.env.PKEYS || "").split(","), // Array of account private keys
          "https://rpc.v2b.testnet.pulsechain.com"
        ),
      gas: 5000000,
      gasPrice: 50000000000,
      network_id: 941, // PulseChain Testnet network id
    },
```
This defines a new network `testnet` which can be used by passed as `--network testnet` arguments to `tuffle`.
Note: Check (main page)[index#Testnets] for current URL to the public PulseChain RPC.

Now create or edit `.env` file in your project directory to have the following content:
```sh
PKEYS="1234...98,ABCD...EF"
```
This file will define private keys into environment variable `PKEYS` passed to the `HDWalletProvider` instance.

Never commit or share the `.env` file! Although you can alternatively set `PKEYS` environment variable in your shell, keep in mind it may be saved with shell's history.
{:.warning}

# Compiler version

You need to use Solidity compiler version at least 0.8.7+. You can install specific `solc` package like this:
```sh
npm install npm install --save-dev solc@0.8.13
```

Then you need to adjust compiler version to be used in `truffle-config.js` under the `module.exports` &rarr; `compilers` &rarr; `solc` &rarr; `version` key.

# Deploy to PulseChain

Before your start make sure you have tPLS or PLS for gas available on your account specified by the `PKEYS` (see above).

```sh
truffle migrate --network testnet
```


# Contract verification

You can use `truffle-plugin-verify` to verify smart contract with [PulseScan](pulsescan). Start by installing the plugin:
```sh
npm install --save-dev truffle-plugin-verify
```

Then edit your `truffle-config.js` and add the following under `module.exports`:
```javascript
  plugins: ['truffle-plugin-verify'],
  api_keys: {
    etherscan: '0'
  },
```

Truffle verification plugin does not support PulseChain, therefore it needs to be patched by editing `./node_modules/truffle-plugin-verify/constants.js` and:
- add to `API_URLS` a new line:
```javascript
941: 'https://scan.v2b.testnet.pulsechain.com/api',
```
- add to `EXPLORER_URLS` a new line:
```javascript
941: 'https://scan.v2b.testnet.pulsechain.com',
```

Then run:
```sh
truffle run verify Migrations --network testnet --debug
```

