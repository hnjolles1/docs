---
title: Bridging LPT from L1 to L2
---

This guide will walk tokenholders through the process of moving LPT from the Ethereum mainnet to Arbitrum and vice versa.

**This only applies if you have not delegated your LPT to an orchestrator on L1**

Please note that you will need some ETH in your wallet to complete this guide. If you are using testnet, you will need Rinkeby ETH.

1. Make sure your wallet (i.e. Metamask) is connected to the Ethereum mainnet (Rinkeby if using testnet) and then navigate to the [Arbitrum Bridge](https://bridge.arbitrum.io/)
2. Click "Token", and enter the L1 LPT contract address ([0x58b6a8a3302369daec383334672404ee733ab239](/protocol/reference/deployed)). If you are using testnet, use the `LivepeerToken` address for Rinkeby.

![BridgeClickToken](https://user-images.githubusercontent.com/89408276/155851894-eb959beb-3269-40b1-8d50-8768bf15f9f2.jpg)

4. Press "enter" on your keyboard. It will take 5-10 seconds for LPT to appear.

![BridgeChooseToken-Enter](https://user-images.githubusercontent.com/89408276/155851630-8e60a17a-b6bd-4a65-972c-53d34c600026.jpg)

6. Select LPT from the dropdown. Once you've done this, you should see your L1 balance.
7. Click `Deposit` to move your L1 LPT to L2. This will initiate an Approval transaction. The first of 2 transactions required to bridge LPT to Arbitrum.
8. After the Approval transaction status changes from pending to success (about 10 mins) LPT can now be Deposited.

![BridgeApprove](https://user-images.githubusercontent.com/89408276/155850572-2337514c-f6ad-419a-a9bf-94e7d3e1d891.jpg)

10. Click 'Deposit' and confirm the transaction in your wallet to complete the bridging of LPT from L1 to L2

![Bridge](https://user-images.githubusercontent.com/89408276/155375033-6fd66e8a-53ab-43e9-9fe6-3a0cec847a55.jpg)

### Bridging from L2 to L1

The same general instructions apply with three differences:

- You should start with your wallet connected to Arbitrum (Arbitrum Rinkeby if using testnet)
- You will need to manually approve the amount that you are bridging using the [Arbiscan UI](https://arbiscan.io/address/0x289ba1701C2F088cf0faf8B3705246331cB8A839#writeContract). To do so, connect your wallet, scroll to the Approve function, enter the L2LPTGateway address [0x6D2457a4ad276000A615295f7A80F79E48CcD318](https://arbiscan.io/address/0x6D2457a4ad276000A615295f7A80F79E48CcD318) and the amount you'll be transferring and click "Write". Note that the amount is in "Wei", so use a [Unit Converter](https://etherscan.io/unitconverter) (e.g. if you want to bridge 10 LPT, you need to enter 10000000000000000000).
  - We are looking into options to remove this step. If it seems complex, we recommend swapping to ETH via [Uniswap](https://app.uniswap.org/#/swap?chain=arbitrum) and then transferring the ETH from L2 to L1 instead.
- Next, click on the arrow below the amount input. The button will switch from "Deposit" to "Withdraw"
- Click on "Token" -> "Manage token lists" -> enable "Arbed CMC List" and click on "Back to Select Token". You should now be able to choose LPT in the list.
- After you've sent the withdraw tx, you need to wait ~1 week until you can claim your LPT on L1. You'll see the countdown below. Once the confirmation period is over, you can connect to the Ethereum mainnet and click "Claim" to withdraw your LPT on L1.

### Bridging Failures

The Arbitrum rollup can fail to bridge tokens correctly if the gas prices are fluctuating by too much, or if the transaction runs out of gas. The former can be caused by gas prices changing from when the original "bridge" transaction was submitted, to when it was sequenced and submitted on L2. In this case, tokens will not reach your L2 wallet after an hour. Arbitrum [provides a page](https://retryable-tx-panel.arbitrum.io/) to check on the status of your L1 deposit transaction:

![Arbitrum UI](/docs-assets/delegators/how-to-guides/arbitrum-retry-ui.png)

In the event of a failure of LPT to bridge due to gas spikes, you should see a message similar to the following:

![Arbitrum UI](https://user-images.githubusercontent.com/23727056/176745651-98ff56d0-9c0a-4c2d-b9fe-bf3ba1d537a7.png)

You should then be able to connect your wallet and resubmit the bridge transaction. This will retry the previous transaction. Make sure to do this in a timely manner, since the [L2 retry buffer is limited](https://github.com/OffchainLabs/arbitrum/blob/master/docs/L1_L2_Messages.md).
