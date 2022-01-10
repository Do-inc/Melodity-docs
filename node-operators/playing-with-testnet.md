---
description: >-
  Playing with testnet is one of the most important things to do for actually
  testing it, so here is a tutorial for trying its functionalities.
---

# Playing with Testnet

## Wallet creation

Creating a wallet is the first step required in order to interact with the _Melodity Beats Testnet_. \
As the blockchain is based on the Substrate framework interacting with it is very similar to interacting with any other Polkadot or Kusama chain.

#### Polkadot.js wallet

The wallet required to directly interact with the blockchain is called `polkadot.js` and is the officially maintained wallet for the whole Polkadot and Kusama environment. You can download it directly from their website [here](https://polkadot.js.org/extension/).

Once you've downloaded and installed the official extension creating a wallet address is as easy as clicking on its icon, pressing the plus icon on the top right of the app and select `create new account`.

You'll be prompted with a screen like the following.

![Creating new account - step 1/2](<../.gitbook/assets/Screenshot from 2022-01-10 21-06-29.png>)

You will be presented with a randomly generated seed phrase, store it in a safe place as you'll need it in order to move your wallet to other devices.

Once you've confirmed you've saved the mnemonic seed a second popup will be shown to you.

![Creating new account - step 2/2](<../.gitbook/assets/Screenshot from 2022-01-10 21-07-09.png>)

Select `allow use on any chain` from the _Network_ dropdown, then proceed to fill the other fields. Define an account name and a password required to sign transactions.

Finally, confirm the creation by clicking on the `add the account ...`

Here it is, you've created your _Melodity Beats Chain_ wallet!

### Funding your wallet

Until a faucet is developed and published the only way to fund your wallet is to send an email to `support [at] melodity [dot] org` asking for a refund of your testnet wallet.

In the body of the email, you'll need to provide your wallet address. As of the time of writing the _Beats Chain Testnet_ is not yet added to the list of Polkadot official testnets you need to do some more steps in order to see your _Beats Chain_ compatible wallet address.

Start by connecting to the official RPC node, to do this we'll use the officially developed _Polkadot Apps Portal_ [website](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Ftestnet-rpc.melodity.org#/accounts). At the given link you'll be asked to connect your _Polkadot.js_ extension, answer `yes` in order to expose your account to the platform.

Now, In order to retrieve your _Beats Chain_ wallet address simply click on your address name and a screen will show on the right of the screen. \
You'll see a screen like the one provided in the following pic.

![Your Beats Chain compatible address](<../.gitbook/assets/image (8).png>)

Copy the two lines under your wallet name and include them in the body of the email you'll send us. As soon as possible we'll answer your email with the confirmation we've funded your testnet account.

### Playing with the testnet

What can you do with the testnet?

Well until the platforms are released you cannot actually do much on the testnet but as you probably know we're a smart contract compatible environment so you can _develop_ and interact with smart contracts.

Unfortunately, the _Polkadot Apps Portal_ is not that user friendly so interacting with them is a bit difficult.\
To start interacting with a smart contract navigate to the `Developer` section using the top navigation bar and then open the`Contracts`. You'll be presented with a page like the following.

![Smart contract page](<../.gitbook/assets/image (2).png>)

In order to interact with a smart contract, you have to import them adding their address we've provided some example smart contracts for you to import and play with.

#### Smart contracts address for you to play on testnet

1. Flipper: Nothing special, a true-false smart contract that let anyone change its value\
   `6kBoBSpVMo5K7dXjCB4kptGLDQbFSnDFfvbAUWBp2nmEH6fV`
2. ERC20: A simple example of a token, it mimics the ERC20 standard\
   `6nKinCsu7Bp1GzRntPV6vE9Zx2NYwD36eLcFxrvPd47w8qZh`
3. Incrementer: A simple and public counter, anyone can increment it\
   `6i26yacFsvjppcqQarGkKnz6ED4Mhg8T87UxGr1L3TL1hoCx`
4. Name resolution: A smart contract that links a name to an onchain address\
   `6iFLVxMZvbtDNXdzUQes19suUXjX6wgHSixvCvx38VwdvgMx`

#### Smart contract ABI

{% file src="../.gitbook/assets/metadata (2).json" %}
Flipper ABI
{% endfile %}

{% file src="../.gitbook/assets/metadata.json" %}
ERC20 token ABI
{% endfile %}

{% file src="../.gitbook/assets/metadata (1).json" %}
Incrementer ABI
{% endfile %}

{% file src="../.gitbook/assets/metadata (3).json" %}
Name resolution ABI
{% endfile %}
