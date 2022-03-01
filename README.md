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

### Reserve Parachain ID
	Resure Parachain ID using [Polkadot-js](https://polkadot.js.org/apps/?rpc=ws%3A%2F%2F127.0.0.1%3A9944#/parachains/parathreads)
	Parachain ids start with 2000

### Configure Parachain for Relay chain and Para ID

	- Generate Plain spec with:
	```sh
	./target/release/parachain-collator build-spec --disable-default-bootnode > rococo-local-parachain-plain.json
	```

	- Update Para ID in Plain Spec file
	```rust
		// --snip--
			"para_id": 2000, // <--- your already registered ID
			// --snip--
				"parachainInfo": {
					"parachainId": 2000 // <--- your already registered ID
      },
  		// --snip--
	```

	- Generate Raw spec file from Plain Spec file
	```sh
	./target/release/parachain-collator build-spec --chain rococo-local-parachain-plain.json --raw --disable-default-bootnode > rococo-local-parachain-2000-raw.json
	```

	- Generate Wasm runtime blob
	```sh
	./target/release/parachain-collator export-genesis-wasm --chain rococo-local-parachain-2000-raw.json > para-2000-wasm
	```

	- Generate Parachain Genesis State (hex encoded)
	```sh
	./target/release/parachain-collator export-genesis-state --chain rococo-local-parachain-2000-raw.json > para-2000-genesis
	```

	- Start Collator Node
	```sh
		./target/release/parachain-collator \
		--alice \
		--collator \
		--force-authoring \
		--chain rococo-local-parachain-2000-raw.json \
		--base-path /tmp/parachain1/alice \
		--port 40333 \
		--ws-port 8844 \
		-- \
		--execution wasm \
		--chain ./rococo-custom-3-raw.json \
		--bootnodes /ip4/127.0.0.1/tcp/30333/p2p/<Bootnode Identifier> \
		--port 30343 \
		--ws-port 9977
	```

	- Second Parachain
	Repeat All the above steps with different Para ID, base path and ports. Use same relay chain spec.
