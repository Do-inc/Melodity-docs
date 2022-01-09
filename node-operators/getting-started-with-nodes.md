# Getting started with nodes

## Why do we need nodes?

We need nodes and node operators because as a decentralized network the Beats Chain needs an operator external to the developer team of Melodity and Do inc.

As a decentralized network is as resilient as more nodes are live we plan to increase the maximum number of nodes during time

## Setting up a node

There are two methods of setting up a node:

* getting the official sources and compiling them using Rust compiler
* getting the official executables and simply run them on your machine

{% hint style="info" %}
Executables are built only for Linux machines, for any other OS you need to compile the sources from scratch using _cargo_.
{% endhint %}

All released versions of the _Melodity Beats Nodes_ can be found on the [official GitHub repository](https://github.com/Do-inc/melodity-beats-chain/releases).&#x20;

In order to run a node download the _melodity-beats-node_ executable in your machine using your preferred method, in the next example, we will use _wget_.

```
wget https://github.com/Do-inc/melodity-beats-chain/releases/download/v2.0.1/melodity-beats-node
```

{% hint style="info" %}
Make sure to always download the latest version (v 2.0.1 at the time of writing).
{% endhint %}

Once you've downloaded the node executable there is one more file needed, it is the configuration file named _chain-conf.raw.json_ it will be included in all the releases even if it will change very little between releases. You may download it with _wget_ as follows.

```
wget https://github.com/Do-inc/melodity-beats-chain/releases/download/v2.0.1/chain-conf.raw.json
```

{% hint style="info" %}
Make sure to always download the latest version (v 2.0.1 at the time of writing).
{% endhint %}

{% hint style="warning" %}
Depending on your system you may need to make the file executable using:\
`chmod +x melodity-beats-node`
{% endhint %}

Once the files are downloaded running a node is as simple as running the next command

```
./melodity-beats-node \ 
--chain chain-conf.raw.json \
--base-path /tmp/<your node name> \
--bootnodes /dns/<boot-node domain>/tcp/30333/p2p/<boot-node identifier> \
--name <your node name>
```

A few notes on the above command:

* It will run a node in live streaming, once you stop the program execution your node will stop
* It sets up the storage folder for your node in the _tmp_ directory, by definition unsafe for permanently storing files
* It set up a backup node, no validation nor reward is given to backup nodes
* _\<your node name>,_ _\<boot-node identifier> and \<boot-node ip>_ are placeholder that must be substituted with real values in order for the node to correctly boot
