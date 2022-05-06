---
title: Smart Contract Development with Remix IDE
---

# Configure Remix for Testnet

Open the `Deploy & run transactions` section in the left navigation bar. Then select the `ENVIRONMENT` to `Web 3 Provider`.  
In the popup window, which should appear, set the `Web3 Provider Endpoint` to value: `https://rpc.v2b.testnet.pulsechain.com` and click the `OK` button.

# Compiler version

You need to use Solidity compiler version at least 0.8.7+. You can change this within the IDE's `Solidity compiler` section:
- Change the `COMPILER` to the value `0.8.7+commit.e28d00a7`.
- Change the `EVM VERSION` to the value `london`.
- Check the `Enable optimization` checkbox and set the next field to `200`.

# Deploy to PulseChain

If you have your contract compiled, open the `Deploy & run transactions` section in the left navigation bar. 

Before your start make sure you have tPLS or PLS for gas available on your account specified by the `ACCOUNT`.

# Contract verification

You need `Flattener` plugin for contract verification. Open the IDE's `Plugin manager` section (2nd bottom button on the left navigation bar) and search for the plugin and activate it by clicking the `Activate` button.  
When you have this plugin activated, a new section icon should appear on the left navigation bar - open this section.

Click the `Flatten ....sol` button and then click the `Save ....sol` button - this will copy the flattened version of the Solidity source code to the clipboard.

Now open PulseScan wizard for contract validation in browser by navigating to [https://scan.v2b.testnet.pulsechain.com/address/0xDEPLOYED_CONTRACT_ADDRESSADDRESS/contract_verifications/new](https://scan.v2b.testnet.pulsechain.com/address/0xDEPLOYED_CONTRACT_ADDRESSADDRESS/contract_verifications/new) .

In the first step of the wizard:
- Ensure that the `Contract Address` is set to the deployment address of your contract.
- Radio button `Via flattened source code` is selected (should be selected by default).

Now click the `Next` button. In the second step of the wizard:
- Fill in the `Contract Name` field.
- Set the `Compiler` to the same version as the value set in the Remix IDE.
- Set the `EVM Version` to the same version as the value set in the Remix IDE.
- Tick `Optimization` to `Yes` and fill in the `Optimization runs` as set in the Remix IDE.
- Paste the flattened contract code into the field `Enter the Solidity Contract Code`.

Now click the `Verify & publish` button, it should change its label to `Loading`.

The verification process can take a lot of time. Meanwhile, the `Loading` button may not change.
While waiting for verification you can also check manually the `Code` tab on PulseScan, if the contract is shown as verified there, you can close the browser.
{:.info}

You can open the `Code` tab on PulseScan by navigating to [https://scan.v2b.testnet.pulsechain.com/address/0xDEPLOYED_CONTRACT_ADDRESSADDRESS/contracts](https://scan.v2b.testnet.pulsechain.com/address/0xDEPLOYED_CONTRACT_ADDRESSADDRESS/contracts) .

