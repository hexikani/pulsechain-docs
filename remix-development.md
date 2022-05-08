---
title: Smart Contract Development with Remix IDE
---

# Compiler version

You need to use Solidity compiler version at least 0.8.7+. You can change this within the IDE's `Solidity compiler` section:
- Change the `COMPILER` to the value `0.8.7+commit.e28d00a7`.
- Change the `EVM VERSION` to the value `london`.
- If you received a warning redarding the contract code size then check the `Enable optimization` checkbox and set the next field to `200` or greater.

Remember the values set here for smart contract verification on the PulseScan later.

# Configure Remix for Testnet

Open the `Deploy & run transactions` section in the left navigation bar. Then select the `ENVIRONMENT` to `Injected Web 3`. If you are connected PulseChain Testnet via the MetaMask wallet, then the text `Custom (941) netword` should be visible in the drop down. Also in the `ACCOUNT` dropdown you should see address of the account from the MetaMask.

# Deploy to PulseChain

If you have your contract compiled, open the `Deploy & run transactions` section in the left navigation bar. 

Before your start make sure you have tPLS or PLS for gas available on your account specified by the `ACCOUNT`.

# Contract verification	

You need `Flattener` plugin for contract verification. Open the IDE's `Plugin manager` section (2nd bottom button on the left navigation bar) and search for the plugin and activate it by clicking the `Activate` button.  
When you have this plugin activated, a new section icon should appear on the left navigation bar - open this section.

Click the `Flatten ....sol` button and then click the `Save ....sol` button - this will copy the flattened version of the Solidity source code to the clipboard.

Proceed with the steps below only if PulseScan already indexed your smart contract and you can see the `Code` tab.
{:.info}

Now open PulseScan wizard for contract validation in browser by navigating to [https://scan.v2b.testnet.pulsechain.com/address/0xDEPLOYED_CONTRACT_ADDRESSADDRESS/contract_verifications/new](https://scan.v2b.testnet.pulsechain.com/address/0xDEPLOYED_CONTRACT_ADDRESSADDRESS/contract_verifications/new) .

In the first step of the wizard:
- Ensure that the `Contract Address` is set to the deployment address of your contract.
- Radio button `Via flattened source code` is selected (should be selected by default).

Now click the `Next` button. In the second step of the wizard:
- Fill in the `Contract Name` field.
- Select `Include nightly builds` to `no`.
- Set the `Compiler` to the same version as the value set in the Remix IDE.
- Set the `EVM Version` to the same version as the value set in the Remix IDE.
- Tick `Optimization` according to your setting in Remix IDEi and fill in the `Optimization runs` as set there.
- Paste the flattened contract code into the field `Enter the Solidity Contract Code`. Make sure you also include your [SPDX License Identifier line](https://docs.soliditylang.org/en/v0.8.7/layout-of-source-files.html).

Now click the `Verify & publish` button, it should change its label to `Loading`.

The verification process can take a lot of time. Meanwhile, the `Loading` button may not change.
While waiting for verification you can also check manually the `Code` tab on PulseScan, if the contract is shown as verified there, you can close the browser.
{:.info}

You can open the `Code` tab on PulseScan by navigating to [https://scan.v2b.testnet.pulsechain.com/address/0xDEPLOYED_CONTRACT_ADDRESSADDRESS/contracts](https://scan.v2b.testnet.pulsechain.com/address/0xDEPLOYED_CONTRACT_ADDRESSADDRESS/contracts) .

