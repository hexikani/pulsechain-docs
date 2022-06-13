---
title: Smart Contract Development with Harthat
---

# Setup Hardhat project

Create a project directory and setup Hardhat according to [tutorial](https://hardhat.org/tutorial/creating-a-new-hardhat-project.html):
```sh
npm init
npm install --save-dev hardhat
```

You can start your development by creating the project:
```sh
npx hardhat
```

# Configure Hardhat for Testnet

We need `dotenv` module relatively security store the private key for deployment:
```sh
npm install --save-dev dotenv
```

Now edit `hardhat.config.js` to include:
- the following line at the global scope:
  ```javascript
  require('dotenv').config(); // read .env file and update process.env if present
  ```
- include the following lines (merge as necessary)
  ```javascript
  module.exports = {
    networks: {
      testnet: {
         chainId: 941,
         url: "https://rpc.v2b.testnet.pulsechain.com",
         accounts: (process.env.PKEYS || '').split(','),
         gasPrice: 50000000000,
      }
    },
    etherscan: { // needed for contract verification
     apiKey: {
        testnet: '0',
     }
    }
  };
  ```

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
npm install solc@0.8.13
```

Then you need to adjust compiler version to be used in `hardhat.config.js` under the `module.exports` &rarr; `solidity` key.

# Deploy to PulseChain

Before your start make sure you have tPLS or PLS for gas available on your account specified by the `PKEYS` (see above). You also need a deploy scripts, in the example below referred to as `scripts/deploy.js`.
You can deploy your smart contracts as:
```sh
npx hardhat run scripts/deploy.js --network testnet
```

Your deploy script should contain some code to print out address of the deployed contract - it usually looks like this:
```javascript
...
const contract = await YourContract.deploy();
console.log("YourContract address:", contract.address);
...
```
You will need the contract's address for contract verification below.

If you intend to verify your contract on PulseScan you have to use Windows line endings in you `.sol` files.
{:.info}

# Contract verification

You can use `hardhat` Etherscan plugin to verify smart contract with [PulseScan](pulsescan). Start by installing the plugin:
```sh
npm install --save-dev @nomiclabs/hardhat-etherscan
```

Then add the following statements to your `hardhat.config.js` at the global scope:
```javascript
require("@nomiclabs/hardhat-etherscan");
const { chainConfig } = require("@nomiclabs/hardhat-etherscan/dist/src/ChainConfig");
chainConfig['testnet'] = {
  chainId: 941,
  urls: {
    apiURL: "https://scan.v2b.testnet.pulsechain.com/api",
    browserURL: "https://scan.v2b.testnet.pulsechain.com",
  },
}
```

Before you proceed with contract verification make sure the contract is indexed by PulseScan. When you open the address of your smart contract in PulseScan you should see your `Contract Creation Success` transaction as well as the `Code` tab.
{:.info}

Now you should be able to verify your contract:
```sh
npx hardhat verify --network testnet 0xDEPLOYED_CONTRACT_ADDRESS <Constructor argument>
```

This command can take a while and there were cases when it got stuck. While waiting for verification you can also check manually the `Code` tab on PulseScan, if the contract is shown as verified there, you can stop the command.
