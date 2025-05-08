
# WalletConnect V1 Example Dapp

**Note**: WalletConnect V1 is being sunset. We recommend using [WalletConnect V2 examples](https://github.com/WalletConnect) for guidance instead of this repository. However, this repository serves as an educational resource for working with WalletConnect V1.
## Develop

To start the local development server, run the following command:

```bash
npm run start
## Table of Contents
1. [Introduction](#walletconnect-v1-example-dapp)
2. [Quick Start](#quick-start)
3. [Develop](#develop)
4. [Test](#test)
5. [Build](#build)
6. [Usage Examples](#usage-examples)
   - [Connecting a Wallet](#connecting-a-wallet)
   - [Sending Transactions](#sending-transactions)
7. [Connecting Wallets](#connecting-wallets)
   - [Connecting Defly Wallet](#connecting-defly-wallet)
   - [Connecting Pera Wallet](#connecting-pera-wallet)
8. [Balance Check](#balance-check)
9. [Handling Fees](#handling-fees)
10. [Payment Request](#payment-request)
11. [Clawback Withdrawal](#clawback-withdrawal)
12. [Options](#options)
13. [Methods](#methods)
14. [Customizing Style](#customizing-style)
15. [Contributing](#contributing)
16. [License](#license)

---

## Quick Start

To get started with this dApp, install the required SDK:

```bash
npm install --save @blockshake/defly-connect
Here’s an example of how to connect and handle wallets:

JavaScript
// Connect handler
deflyWallet
  .connect()
  .then((newAccounts) => {
    // Set up the disconnect event listener
    deflyWallet.connector?.on("disconnect", handleDisconnectWalletClick);

    setAccountAddress(newAccounts[0]);
  })
  .catch((error) => {
    // Handle the rejection when the user closes the modal
    if (error?.data?.type !== "CONNECT_MODAL_CLOSED") {
      console.error("Connection error:", error);
    }
  });

// On every page refresh, reconnect the session
deflyWallet.reconnectSession().then((accounts) => {
  deflyWallet.connector?.on("disconnect", handleDisconnectWalletClick);

  if (accounts.length) {
    setAccountAddress(accounts[0]);
  }
});
Develop
To start the local development server, run the following command in your terminal:

bash
npm run start
This will start the application in development mode. You can open it in your browser at http://localhost:3000.

Test
Run the following command to execute the project's test suite:

bash
npm run test
The test suite ensures that the WalletConnect integration and other functionalities work as expected.

Build
To create a production-ready build of the project, use the following command:

bash
npm run build
This will generate optimized files for deployment in the build/ directory.

Usage Examples
Connecting a Wallet
Here’s how you can connect a wallet using WalletConnect V1:

JavaScript
const walletConnect = new WalletConnect({
  bridge: "https://bridge.walletconnect.org", // Required
});

// Check if already connected
if (!walletConnect.connected) {
  // Create a new session
  walletConnect.createSession();
}

// Subscribe to connection events
walletConnect.on("connect", (error, payload) => {
  if (error) {
    throw error;
  }

  // Get provided accounts
  const { accounts } = payload.params[0];
  console.log("Connected accounts:", accounts);
});

// Subscribe to disconnection events
walletConnect.on("disconnect", (error, payload) => {
  if (error) {
    throw error;
  }

  console.log("Wallet disconnected");
});
Sending Transactions
Below is an example of how to send a transaction using WalletConnect:

JavaScript
async function sendTransaction(walletConnect, from, to, amount) {
  const tx = {
    from: from,
    to: to,
    value: amount, // Amount in wei
    gasPrice: "0x09184e72a000", // Customize based on network
    gas: "0x2710", // Customize based on transaction
  };

  try {
    const signedTx = await walletConnect.signTransaction(tx);
    console.log("Signed Transaction:", signedTx);

    // Send the transaction
    const receipt = await walletConnect.sendTransaction(signedTx);
    console.log("Transaction Receipt:", receipt);
  } catch (error) {
    console.error("Transaction failed:", error);
  }
}
Connecting Wallets
Connecting Defly Wallet
JavaScript
// Connect Defly Wallet
deflyWallet.connect().then((accounts) => {
  console.log("Connected accounts:", accounts);
}).catch((error) => {
  console.error("Error connecting Defly Wallet:", error);
});
Connecting Pera Wallet
JavaScript
// Connect Pera Wallet
peraWallet.connect().then((accounts) => {
  console.log("Connected accounts:", accounts);
}).catch((error) => {
  console.error("Error connecting Pera Wallet:", error);
});
Balance Check
JavaScript
// Check account balance
async function getAccountBalance(accountAddress) {
  const algodClient = new AlgodClient("https://testnet-algorand.api.purestake.io/ps2", "Your-API-Token");
  // Replace "Your-API-Token" with your actual API token.
  const accountInfo = await algodClient.accountInformation(accountAddress).do();
  console.log("Account balance:", accountInfo.amount);
}
Handling Fees
JavaScript
// Specify transaction fees
const fee = 1000; // in microAlgos
const txnParams = {
  fee: fee,
  flatFee: true,
  firstRound: 10000,
  lastRound: 10100,
  genesisID: "testnet-v1.0",
  genesisHash: "Your-Genesis-Hash", // Replace with your network's genesis hash.
  sender: "Your-Address", // Replace with your wallet address.
  receiver: "Recipient-Address", // Replace with the recipient's wallet address.
  amount: 1000000, // 1 Algo
  note: new Uint8Array(Buffer.from("Transaction Note")),
};
Payment Request
JavaScript
// Construct and sign a payment transaction
const paymentTxn = algosdk.makePaymentTxnWithSuggestedParamsFromObject({
  from: "Your-Address", // Replace with your wallet address.
  to: "Recipient-Address", // Replace with the recipient's wallet address.
  amount: 1000000, // 1 Algo
  note: new Uint8Array(Buffer.from("Payment Note")),
  suggestedParams: txnParams,
});

const signedTxn = await deflyWallet.signTransaction([paymentTxn]);
await algodClient.sendRawTransaction(signedTxn).do();
console.log("Payment sent successfully");
Clawback Withdrawal
JavaScript
// Clawback transaction example
const clawbackTxn = algosdk.makeAssetTransferTxnWithSuggestedParamsFromObject({
  from: "Clawback-Address", // Replace with the clawback wallet address.
  to: "Recipient-Address", // Replace with the recipient's wallet address.
  assetIndex: 123456, // Replace with your asset ID.
  amount: 100000, // Amount to withdraw.
  revocationTarget: "Revoked-Address", // Replace with the revoked wallet address.
  suggestedParams: txnParams,
});

const signedClawbackTxn = await deflyWallet.signTransaction([clawbackTxn]);
await algodClient.sendRawTransaction(signedClawbackTxn).do();
console.log("Clawback transaction executed successfully");
Options
Option	Default	Value	Description
chainId	4160	416001, 416002, 416003, 4160	Determines the network to use.
shouldShowSignTxnToast	true	boolean	Show toast during transactions.
Methods
DeflyWalletConnect.connect(): Promise<string[]>
Starts the initial connection flow and returns an array of account addresses.

DeflyWalletConnect.reconnectSession(): Promise<string[]>
Reconnects to the wallet if there is any active connection and returns an array of account addresses.

DeflyWalletConnect.disconnect(): Promise<void | undefined>
Disconnects from the wallet and resets the related storage items.

DeflyWalletConnect.signTransaction(txGroups: SignerTransaction[][], signerAddress?: string): Promise<Uint8Array[]>
Starts the sign process and returns the signed transaction in Uint8Array.

## License

This project is licensed under the MIT License. See the [LICENSE](./LICENSE) file for details.
