# Simple-XCMP-Counter
Simple XCMP application to set and update counter of different parachain

## Step1 Building the relay Chain node.

```sh
# Clone the Polkadot Repository
$ git clone https://github.com/paritytech/polkadot.git

# Switch into the Polkadot directory
$ cd polkadot

# Checkout the proper commit
git checkout v0.9.16

# Build the relay chain Node
$ cargo build --release

# Check if the help page prints to ensure the node is built correctly
$ ./target/release/polkadot --help

```

- Generate relay chain spec file.
There must be minimum 2 validator for 1 parachain. In this project we will be using two parachains , so therefore we will require 3 validator nodes
Check here to learn more [here](https://docs.substrate.io/v3/runtime/chain-specs/).
```sh
./target/release/polkadot build-spec \
--chain rococo-local \
--raw \
--disable-default-bootnode \
> rococo_local.json
```

You can use pre configured chain spec file for testing from [here]()

### Start relay node with 3 validator Alice, Bob and Charlie
- Alice
```sh
./target/release/polkadot \
--alice \
--validator \
--base-path /tmp/relay/alice \
--chain ./rococo-custom-3-raw.json \
--port 30333 \
--ws-port 9944
```

- Bob
```sh
./target/release/polkadot \
--bob \  
--validator \
--base-path /tmp/relay-bob \
--chain ./rococo-custom-3-raw.json \
--bootnodes /ip4/127.0.0.1/tcp/30333/p2p/<ALICE NODE IDENTIFIER> \
--port 30334 \
--ws-port 9945
```

- Charlie
```sh
./target/release/polkadot \
--charlie \
--validator \
--base-path /tmp/relay-charlie \
--chain /Users/salman01z/Crypto/parachains/chain_spec_files/rococo-custom-3-raw.json \
--bootnodes /ip4/127.0.0.1/tcp/30333/p2p/<ALICE NODE IDENTIFIER>\
--port 30335 \
--ws-port 9946

```

## Step2 Build Parachain

