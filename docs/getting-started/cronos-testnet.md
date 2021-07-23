---
meta:
  - name: "title"
    content: Cronos | Crypto.org EVM Chain | Running Nodes On Testnet
  - name: "description"
    content: Learn how to setup a Validator or a full node on Crypto.org Cronos testnet cronostestnet-338 in this technical documentation.
  - name: "og:title"
    content: Cronos | Crypto.org EVM Chain | Running Nodes On Testnet
  - name: "og:type"
    content: Website
  - name: "og:description"
    content: Learn how to setup a Validator or a full node on Crypto.org Cronos testnet cronostestnet-338 in this technical documentation.
  - name: "og-image"
    content: https://cronos.crypto.org/og-image.png
  - name: "twitter:title"
    content: Cronos | Crypto.org EVM Chain | Running Nodes On Testnet
  - name: "twitter:site"
    content: "@cryptocom"
  - name: "twitter:card"
    content: summary_large_image
  - name: "twitter:description"
    content: Learn how to setup a Validator or a full node on Crypto.org Cronos testnet cronostestnet-338 in this technical documentation.
  - name: "twitter-image"
    content: https://cronos.crypto.org/og-image.png
---

# Cronos Testnet: Running Nodes

<h2 class="normal-text">
  The latest Crypto.org Chain Testnet has been named as <b>Cronos</b>.
</h2>

<h2 class="normal-text no-margin-top">
  This is a detailed documentation for setting up a Validator or a full node on Crypto.org Cronos testnet <code>cronostestnet-338</code>.
</h2>

## Pre-requisites

### Supported OS

We officially support macOS, Windows and Linux only. Other platforms may work but there is no guarantee. We will extend our support to other platforms after we have stabilized our current architecture.

### Prepare your machine

- To run Cronos testnet nodes, you will need a machine with the following minimum requirements:

  - 4-core, x86_64 architecture processor;
  - 16 GB RAM;
  - 1 TB of storage space.

## Step 1. Get the Cronos testnet binary

::: tip Remarks:
The following is the minimal setup for a **validator node** / **full node**.
:::

To simplify the following step, we will be using **Linux** (Intel x86) for illustration. Binary for
**Mac** ([Intel x86](https://github.com/crypto-org-chain/cronos/releases/download/v0.5.0-rc0/ethermint_0.5.0-rc0_Darwin_x86_64.tar.gz) / [M1](https://github.com/crypto-org-chain/cronos/releases/download/v0.5.0-rc0/ethermint_0.5.0-rc0_Darwin_arm64.tar.gz))and [Windows](https://github.com/crypto-org-chain/cronos/releases/download/v0.5.0-rc0/ethermint_0.5.0-rc0_Windows_x86_64.zip) are also available.

- To install released **Cronos testnet binaries** from github:

  ```bash
  $ curl -LOJ https://github.com/crypto-org-chain/cronos/releases/download/v0.5.0-rc0/ethermint_0.5.0-rc0_Linux_x86_64.tar.gz
  $ tar -zxvf ethermint_0.5.0-rc0_Linux_x86_64.tar.gz
  ```

  Afterward, you can check the version of `ethermintd` by

  ```bash
  $ ./ethermintd version
  0.5.0-rc0
  ```

## Step 2. Configure `ethermintd`

Before kick-starting your node, we will have to configure your node so that it connects to the Cronos testnet:

### Step 2-1 Initialize `ethermintd`

- First of all, you can initialize ethermintd by:


  ```bash
    $ ./ethermintd init [moniker] --chain-id cronostestnet-338
  ```



  This `moniker` will be the displayed id of your node when connected to Crypto.org Chain network.
  When providing the moniker value, make sure you drop the square brackets since they are not needed.
  The example below shows how to initialize a node named `pegasus-node` :


  ```bash
    $ ./ethermintd init pegasus-node --chain-id cronostestnet-338
  ```



  ::: tip NOTE

  - Depending on your ethermintd home setting, the ethermintd configuration will be initialized to that home directory. To simply the following steps, we will use the default ethermintd home directory `~/.ethermintd/` for illustration.
  - You can also put the `ethermintd` to your binary path and run it by `ethermintd`
    :::

### Step 2-2 Configure ethermintd



- Download and replace the Cronos Testnet `genesis.json` by:

  ```bash
  $ curl https://raw.githubusercontent.com/crypto-org-chain/cronos-testnets/master/cronostestnet-338/genesis.json > ~/.ethermintd/config/genesis.json
  ```


- Verify sha256sum checksum of the downloaded `genesis.json`. You should see `OK!` if the sha256sum checksum matches.


  ```bash
  $ if [[ $(sha256sum ~/.ethermintd/config/genesis.json | awk '{print $1}') = "ce015fe82b6ef598a1bce3d28ecb09859fab0e6e765f4efc97780cfeacbfeefb" ]]; then echo "OK"; else echo "MISMATCHED"; fi;

  OK!
  ```



  ::: tip NOTE

  - For Mac environment, `sha256sum` was not installed by default. In this case, you may setup `sha256sum` with this command:

    ```bash
    function sha256sum() { shasum -a 256 "$@" ; } && export -f sha256sum
    ```

    :::

- In `~/.ethermintd/config/app.toml`, update minimum gas price to avoid [transaction spamming](https://github.com/cosmos/cosmos-sdk/issues/4527)

  ```bash
  $ sed -i.bak -E 's#^(minimum-gas-prices[[:space:]]+=[[:space:]]+).*$#\1"0.025basetcro"#' ~/.ethermintd/config/app.toml
  ```

- For network configuration, in `~/.ethermintd/config/config.toml`, please modify the configurations of `persistent_peers`, `create_empty_blocks_interval` and `timeout_commit` by:
  ```bash
  $ sed -i.bak -E 's#^(persistent_peers[[:space:]]+=[[:space:]]+).*$#\1"6760167d71f53db880dd6a99e37b17db876697c5@13.213.254.205:26656,81348744600631ed3e60b1983e070ed4f190efc5@18.139.32.246:26656"#' ~/.ethermintd/config/config.toml
  $ sed -i.bak -E 's#^(create_empty_blocks_interval[[:space:]]+=[[:space:]]+).*$#\1"5s"#' ~/.ethermintd/config/config.toml
  $ sed -i.bak -E 's#^(timeout_commit[[:space:]]+=[[:space:]]+).*$#\1"8s"#' ~/.ethermintd/config/config.toml
  ```

::: tip NOTE

- For Mac environment, if `jq` is missing, you may install it by: `brew install jq`
  :::

## Step 3. Run everything

::: warning CAUTION
This page only shows the minimal setup for validator / full node.

Furthermore, you may want to run full nodes
as sentries (see [Tendermint](https://docs.tendermint.com/master/tendermint-core/running-in-production.html)), restrict your validator connections to only connect to your full nodes, test secure storage of validator keys etc.
:::

### Step 3-1. Create a new key and address

Run the followings to create a new key. For example, you can create a key with the name `Default` by:

```bash
  $ ./ethermintd keys add Default
```

You should obtain an address with `eth` prefix, e.g. `eth10u5mgfflasrfj9s94mt8l9yucrt2gzhcyt5tsg`. This will be the address for performing transactions.

### Step 3-2. Obtain test token

Users can the [faucet](https://cronos.crypto.org/faucet) to obtain test tokens, please note that you would need a Ethereum type address `0x...` that can be obtained by

- [Using metamask](./metamask.md#using-metamask); or
- Using the [address convention tool](./metamask.md#address-conventions).

In case you have reached the daily limit on faucet , you can simply send a message on [Discord](https://discord.gg/pahqHz26q4) #request-tcro channel ,
stating who you are and your `eth.....` address.

### Step 3-3. Run everything

Once the `ethermintd` has been configured, we are ready to start the node and sync the blockchain data:

- Start ethermintd, e.g.:

```bash
  $ ./ethermintd start
```

::: tip Remarks:
If you see errors saying `too many files opened...`, then you need to set a higher number for maximum open file descriptors in your OS.

If you are on OSX or Linux, then the following could be useful:

```bash
# Check current max fd
$ ulimit -n
# Set a new max fd
$ ulimit -Sn [NEW_MAX_FILE_DESCRIPTOR]
# Example
$ ulimit -Sn 4096
```

:::

- _(Optional for Linux)_ Start ethermintd with systemd service, e.g.:

```bash
  $ curl -s https://raw.githubusercontent.com/crypto-org-chain/cronos-docs/master/systemd/create-service.sh -o create-service.sh && curl -s https://raw.githubusercontent.com/crypto-org-chain/cronos-docs/master/systemd/ethermintd.service.template -o ethermintd.service.template
  $ chmod +x ./create-service.sh && ./create-service.sh
  $ sudo systemctl start ethermintd
  # view log
  $ journalctl -u ethermintd -f
```

:::details Example: /etc/systemd/system/ethermintd.service created by script

```bash
# /etc/systemd/system/ethermintd.service
[Unit]
Description=ethermintd
ConditionPathExists=/usr/local/bin/ethermintd
After=network.target

[Service]
Type=simple
User=ubuntu
WorkingDirectory=/usr/local/bin
ExecStart=/usr/local/bin/ethermintd start --home /home/ubuntu/.ethermintd
Restart=on-failure
RestartSec=10
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
```

:::

It should begin fetching blocks from the other peers. Please wait until it is fully synced before moving onto the next step.

- You can query the node syncing status by

  ```bash
  $ ./ethermintd status 2>&1 | jq '.SyncInfo.catching_up'
  ```

  If the above command returns `false`, It means that your node **is fully synced**; otherwise, it returns `true` and implies your node is still catching up.

- One can check the current block height by querying the public full node by:

  ```bash
  curl -s https://cronos-testnet.crypto.org:26657/commit | jq "{height: .result.signed_header.header.height}"
  ```

  and you can check your node's progress (in terms of block height) by

  ```bash
  $ ./ethermintd status 2>&1 | jq '.SyncInfo.latest_block_height'
  ```

The next step only applied to the validators, in which, validator hosting is by invitation only at the early stage of the Cronos testnet.

---

### Step 3-4. Obtain the validator public key

You can obtain your validator public key by:

```bash
  $ ./ethermintd tendermint show-validator
```

The public key should in a json format, for example:

```json
{
  "@type": "/cosmos.crypto.ed25519.PubKey",
  "key": "gvPPVShkWjuUn7cuqS3ci9fHnC+nLFxzsNWkwGJ6iMI="
}
```

### Step 3-5. Send a `create-validator` transaction

Once the node is fully synced, we are now ready to send a `create-validator` transaction and join the network, for example:

```
$ ./ethermintd tx staking create-validator \
--from=[name_of_your_key] \
--amount=10000000stake \
--pubkey='{"@type":"/cosmos.crypto.ed25519.PubKey","key":"PUBLIC_KEY"}'  \
--moniker="[The_id_of_your_node]" \
--security-contact="[security contact email/contact method]" \
--chain-id="cronostestnet-338" \
--commission-rate="0.10" \
--commission-max-rate="0.20" \
--commission-max-change-rate="0.01" \
--min-self-delegation="1" \
--fees=5000basetcro

{"body":{"messages":[{"@type":"/cosmos.staking.v1beta1.MsgCreateValidator"...}
confirm transaction before signing and broadcasting [y/N]: y
```

You will be required to insert the following:


- `--from`: The `eth...` address that holds your funds;
- `--pubkey`: The validator public key( See Step [3-4](#step-3-4-obtain-the-validator-public-key) above )
- `--moniker`: A moniker (name) for your validator node;
- `--security-contact`: Security contact email/contact method.

### Step 3-6. Check your validator status

Once the `create-validator` transaction completes, you can check if your validator has been added to the validator set:

```bash

$ ./ethermintd tendermint show-validator
## [{"@type":"/cosmos.crypto.ed25519.PubKey","key":"VALIDATOR_KEY"}] ##
$ ./ethermintd query tendermint-validator-set | grep -c [VALIDATOR_KEY]

## 1 = Yes; 0 = Not yet added ##
```

To further check if the validator is signing blocks, kindly run this [script](https://github.com/crypto-com/chain-docs/blob/master/docs/getting-started/assets/signature_checking/check-validator-up.sh), for example:

```bash
$ curl -sSL https://raw.githubusercontent.com/crypto-com/chain-docs/master/docs/getting-started/assets/signature_checking/check-validator-up.sh | bash -s -- \
--tendermint-url https://cronos-testnet.crypto.org:26657 \
--pubkey $(cat ~/.ethermintd/config/priv_validator_key.json | jq -r '.pub_key.value')

The validator is in the active validator set under the address  <YOUR_VALIDATOR_ADDRESS>
The validator is signing @ Block#<BLOCK_HEIGHT> 👍
```


## Step 4. Perform Transactions

### Step 4-1. `query bank balances` - Check your transferable balance

You can check your _transferable_ balance with the `balances` command under the bank module.
:::details Example: Check your address balance

```bash
$ ./ethermintd query bank balances eth1qsklxwt77qrxur494uvw07zjynu03dq9alwh37

balances:
- amount: "10005471622381693"
  denom: basetcro
pagination:
  next_key: null
  total: "0"

```

:::



### Slashing related transaction

### `tx slashing unjail` - Unjail a validator

Validator could be punished and jailed due to network misbehaviour, we can check the jailing status of a validator, for example:

```bash
$ ./ethermintd query staking validators -o json | jq
................................
      "operator_address": "ethvaloper1s9e3f5y4tt4fkz3jyj865qaud2cqhs66qkw5yl",
      "consensus_pubkey": {
        "@type": "/cosmos.crypto.ed25519.PubKey",
        "key": "rXphE0lECaU4MfBL70l6tGrfaply4dp79g7ql4ijfco="
      },
      "jailed": false,
................................
```

Where `"jailed": true` implies that the validator has been jailed. After the jailing period has passed, one can broadcast a `unjail` transaction to unjail the validator and resume its normal operations by

```bash
$ ./ethermintd tx slashing unjail --from [key_name] --chain-id "cronostestnet-338" --gas-prices 0.1basetcro

  {"body":{"messages":[{"@type":"/cosmos.slashing.v1beta1.MsgUnjail"...}]}
  confirm transaction before signing and broadcasting [y/N]: y
```

:::

Congratulations! You've successfully set up a Testnet node and performed some basic transactions! You may refer to [Wallet Management](https://cronos.crypto.org/docs/wallets/cli.html#ethermintd) for more advanced operations and transactions.

## Cronos testnet faucet and explorer



- You can lookup data within the `cronostestnet-338` network by the [explorer](https://cronos-explorer.crypto.org/);

- To interact with the blockchain, simply use the [test-token faucet](https://cronos.crypto.org/faucet) to obtain test CRO tokens for performing transactions on the **Cronos** testnet.

  - Note that you will need to create an [address](#step-3-1-create-a-new-key-and-address) before using the faucet.

