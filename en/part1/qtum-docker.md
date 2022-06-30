# QTUM Docker Container

Download the latest container image:

```
docker pull qtum/qtum:latest
```

## Running regtest Mode

For development and testing purposes it is most convenient to run a local blockchain, so the transactions you make are processed cheaply on your own computer. You can run a local blockchain easily using `qtumd`'s builtin `regtest` mode (short for "regression test").

`regtest` is similar to Ethereum's `testrpc`, but different in a few ways:

+ The blockchain state is stored on disk, and persists across qtumd restarts.
+ The blocks are not mined immediately, but at a semi-regular intervals.
+ Need to manually seed the chain with 2000 blocks.

By default, the `qtum/qtum` docker image will start `qtumd` in `mainnet` mode so we need to specify `-regtest` along with some flags to index the blockchain

```
docker run -it --rm \
  --name qtumd_regtest \
  -v `pwd`:/root \
  -p 23888:23888 \
  qtum/qtum \
  qtumd -regtest -txindex -addrindex=1 -logevents \
  -rpcbind=0.0.0.0:3889 -rpcallowip=0.0.0.0/0 -rpcuser=qtum -rpcpassword=testpasswd
```

+ `--name` Name of the container. You can change this to whatever.
+ `-it` Allocates a terminal device for the container.
+ `--rm` Remove the container immediately after exit.
+ `-v` Maps the current directory as `/dapp` inside the container.
+ `-p` Expose container ports for access from the host OS.
+ `-regtest` Enable regtest mode (`-testnet` also works)
+ `-txindex` Enable indexing transactions
+ `-addrindex=1` Keep track of all account's balances
+ `-logevents` Index EVM events
+ `-rpcbind` Specify port to bind RPC to
+ `-rpcallowip` Whitelist who can call the RPC
+ `-rpcuser` Username to call RPC with
+ `-rpcpassword` Password to call RPC with

You should see output like this:

```
2022-06-30T21:40:11Z Qtum Core version v22.1 (release build)
2022-06-30T21:40:11Z Validating signatures for all blocks.
2022-06-30T21:40:11Z Setting nMinimumChainWork=0000000000000000000000000000000000000000000000000000000000000000
2022-06-30T21:40:11Z Using the 'shani(1way,2way)' SHA256 implementation
2022-06-30T21:40:11Z Using RdSeed as additional entropy source
2022-06-30T21:40:11Z Using RdRand as an additional entropy source
2022-06-30T21:40:11Z Default data directory /root/.qtum
2022-06-30T21:40:11Z Using data directory /root/.qtum/regtest
2022-06-30T21:40:11Z Config file: /root/.qtum/qtum.conf (not found, skipping)
2022-06-30T21:40:11Z Command-line arg: regtest=""
2022-06-30T21:40:11Z Using at most 125 automatic connections (1048576 file descriptors available)
2022-06-30T21:40:11Z Using 16 MiB out of 32/2 requested for signature cache, able to store 524288 elements
2022-06-30T21:40:11Z Using 16 MiB out of 32/2 requested for script execution cache, able to store 524288 elements
2022-06-30T21:40:11Z Script verification uses 7 additional threads
2022-06-30T21:40:11Z scheduler thread start
2022-06-30T21:40:11Z libevent: getaddrinfo: address family for nodename not supported
2022-06-30T21:40:11Z Binding RPC on address ::1 port 13889 failed.
2022-06-30T21:40:11Z HTTP: creating work queue of depth 16
2022-06-30T21:40:11Z Using random cookie authentication.
2022-06-30T21:40:11Z Generated RPC authentication cookie /root/.qtum/regtest/.cookie
2022-06-30T21:40:11Z HTTP: starting 4 worker threads
2022-06-30T21:40:11Z Using wallet directory /root/.qtum/regtest/wallets
2022-06-30T21:40:11Z init message: Verifying wallet(s)…
2022-06-30T21:40:11Z init message: Loading banlist…
2022-06-30T21:40:11Z Missing or invalid file /root/.qtum/regtest/banlist.dat
2022-06-30T21:40:11Z Recreating the banlist database
2022-06-30T21:40:11Z SetNetworkActive: true
2022-06-30T21:40:11Z Failed to read fee estimates from /root/.qtum/regtest/fee_estimates.dat. Continue anyway.
2022-06-30T21:40:11Z Using /16 prefix for IP bucketing
2022-06-30T21:40:11Z Cache configuration:
2022-06-30T21:40:11Z * Using 2.0 MiB for block index database
2022-06-30T21:40:11Z * Using 8.0 MiB for chain state database
2022-06-30T21:40:11Z * Using 440.0 MiB for in-memory UTXO set (plus up to 286.1 MiB of unused mempool space)
2022-06-30T21:40:11Z init message: Loading block index…
2022-06-30T21:40:11Z Switching active chainstate to Chainstate [ibd] @ height -1 (null)
2022-06-30T21:40:11Z Opening LevelDB in /root/.qtum/regtest/blocks/index
2022-06-30T21:40:11Z Opened LevelDB successfully
2022-06-30T21:40:11Z Using obfuscation key for /root/.qtum/regtest/blocks/index: 0000000000000000
```

To terminate the container, hit `ctrl-c` in the terminal, and you should see some more cleanup logs:

```
2022-06-30T21:40:12Z tor: Thread interrupt
2022-06-30T21:40:12Z Shutdown: In progress...
2022-06-30T21:40:12Z opencon thread exit
2022-06-30T21:40:12Z addcon thread exit
2022-06-30T21:40:12Z torcontrol thread exit
2022-06-30T21:40:12Z net thread exit
2022-06-30T21:40:12Z msghand thread exit
2022-06-30T21:40:12Z DumpAnchors: Flush 0 outbound block-relay-only peer addresses to anchors.dat started
2022-06-30T21:40:12Z DumpAnchors: Flush 0 outbound block-relay-only peer addresses to anchors.dat completed (0.01s)
2022-06-30T21:40:12Z scheduler thread exit
2022-06-30T21:40:12Z Writing 0 unbroadcast transactions to disk.
2022-06-30T21:40:12Z Dumped mempool: 3.3e-05s to copy, 0.007701s to dump
2022-06-30T21:40:12Z FlushStateToDisk: write coins cache to disk (0 coins, 0kB) started
2022-06-30T21:40:12Z FlushStateToDisk: write coins cache to disk (0 coins, 0kB) completed (0.00s)
2022-06-30T21:40:12Z FlushStateToDisk: write coins cache to disk (0 coins, 0kB) started
2022-06-30T21:40:12Z FlushStateToDisk: write coins cache to disk (0 coins, 0kB) completed (0.00s)
2022-06-30T21:40:12Z Shutdown: done
```

You should see that the blockchain database for `regtest` had been created in your local project directory at the path `.qtum/regtest`:

```
ls .qtum/regtest

anchors.dat   chainstate         indexes      settings.json  wallets
banlist.json  debug.log          mempool.dat  stateQtum
blocks        fee_estimates.dat  peers.dat    vm.log
```

Let's restart the container, and `qtumd` should reuse the blockchain database saved in `.qtum`.

```
docker run -it --rm \
  --name qtumd_regtest \
  -v `pwd`:/root \
  -p 23888:23888 \
  qtum/qtum \
  qtumd -regtest -txindex -addrindex=1 -logevents \
  -rpcbind=0.0.0.0:3889 -rpcallowip=0.0.0.0/0 -rpcuser=qtum -rpcpassword=testpasswd
```

#### Running Testnet

You can run the test network by passing `-testnet` instead of `-regtest`

Note that syncing Mainnet/Testnet with `-txindex -addrindex=1 -logevents` can take 12+ hours to fully sync

Starting without these flags will significantly reduce sync time but these flags are required when using Janus - our Ethereum JSON-RPC compatibility layer

```
docker run -it --rm \
  --name qtumd_testnet \
  -v `pwd`:/root \
  -p 23888:23888 \
  qtum/qtum \
  qtumd -testnet -txindex -addrindex=1 -logevents \
  -rpcbind=0.0.0.0:3889 -rpcallowip=0.0.0.0/0 -rpcuser=qtum -rpcpassword=testpasswd
```

## Shell Access Into The Container

Let's verify that the container is running:

```
docker ps
```

```
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS         PORTS                                                                           NAMES
78825ef9fbd7   qtum/qtum   "qtumd -regtest -txi…"   4 seconds ago   Up 3 seconds   3888-3889/tcp, 13888-13889/tcp, 0.0.0.0:23888->23888/tcp, :::23888->23888/tcp   qtumd_regtest
```

The container's name is `qtumd_regtest`. We can gain shell access:

```
docker exec -it qtumd_regtest sh
```

The `/root` directory inside the container should be the same as your project directory.

Inside the container, we can use the `qtum-cli` command to interact with qtumd. To get some basic info about the blockchain state:

```
qtum-cli -rpcuser=qtum -rpcpassword=testpasswd getblockchaininfo

{
  "chain": "regtest",
  "blocks": 0,
  "headers": 0,
  "bestblockhash": "665ed5b402ac0b44efc37d8926332994363e8a7278b7ee9a58fb972efadae943",
  "difficulty": 4.656542373906925e-10,
  "moneysupply": 0,
  "mediantime": 1504695029,
  "verificationprogress": 1,
  "initialblockdownload": true,
  "chainwork": "0000000000000000000000000000000000000000000000000000000000000002",
  "size_on_disk": 383,
  "pruned": false,
  "softforks": {
    "bip34": {
      "type": "buried",
      "active": true,
      "height": 0
    },
    "bip66": {
      "type": "buried",
      "active": true,
      "height": 0
    },
    "bip65": {
      "type": "buried",
      "active": true,
      "height": 0
    },
    "csv": {
      "type": "buried",
      "active": false,
      "height": 432
    },
    "segwit": {
      "type": "buried",
      "active": true,
      "height": 0
    },
    "testdummy": {
      "type": "bip9",
      "bip9": {
        "status": "defined",
        "start_time": 0,
        "timeout": 9223372036854775807,
        "since": 0,
        "min_activation_height": 0
      },
      "active": false
    },
    "taproot": {
      "type": "bip9",
      "bip9": {
        "status": "active",
        "start_time": -1,
        "timeout": 9223372036854775807,
        "since": 0,
        "min_activation_height": 0
      },
      "height": 0,
      "active": true
    }
  },
  "warnings": ""
}
```

# New Blocks On Demand

The chain is initially empty. As we have seen in `getblockchaininfo`, there is no block yet:

```
"blocks": 0
```

Furthermore, starting with v22.1, you need to create a wallet and your initial wallet balance is 0:

```
qtum-cli -rpcuser=qtum -rpcpassword=testpasswd getbalance
error code: -18
error message:
No wallet is loaded. Load a wallet using loadwallet or create a new one with createwallet. (Note: A default wallet is no longer automatically created)
```

```
qtum-cli -rpcuser=qtum -rpcpassword=testpasswd createwallet wallet
{
  "name": "wallet",
  "warning": ""
}
```

```
qtum-cli -rpcuser=qtum -rpcpassword=testpasswd getbalance
0.00000000
```

In `regtest` mode, you are allowed to mine new blocks for testing purposes. This is useful in two ways:

1. Generate enough balance to pay for transactions.
2. To confirm transactions quickly instead of waiting for a new block to be mined.

Let's seed the chain with 2001 blocks:

```
qtum-cli -rpcuser=qtum -rpcpassword=testpasswd -generate 2001
```

```
[
  // more ...
  "43e9c190679a4d040d07c6d0d5d34c1d49f7b6b6539ceb87eea76af3ef39eed5",
  "7d5b6a0e5e76cf18e35ac18d4534155a0fd96201feaefc720118452dd50bcd5b",
  "70013fa6e01ad527e1f71033ebf59c5233877fcd0fd8233ea032ddaface3cbbc",
  "6c73fe21f7c7874b550e190dcc69320c13a96f188bfb89ab858bc42adc0a1398",
  "5b48289b22fbf073f6a345d97968c3b8ee44381e8e10e6f940d31b7355684968",
  "4f9444d8bd3ece4f6839d7bcfc7f964e95187e2868128681b7bc4cb42d719b41"
]
```

Now your balance is non-zero:

```
qcli getbalance

20000.00000000
```

And number of blocks is about 20001ish:

```
qtum-cli -rpcuser=qtum -rpcpassword=testpasswd getblockchaininfo

"blocks": 2001,
```

> Note: QTUM's proof-of-stake reward is unspendable until it "matures" after 2000 blocks. By generating 2001 blocks, we get 1 matured block rewards, of 20k QTUM each.

# Summary

In this chapter we have run qtumd in a docker container:

```
docker run -it --rm \
  --name qtumd_regtest \
  -v `pwd`:/root \
  -p 23888:23888 \
  qtum/qtum \
  qtumd -regtest -txindex -addrindex=1 -logevents \
  -rpcbind=0.0.0.0:3889 -rpcallowip=0.0.0.0/0 -rpcuser=qtum -rpcpassword=testpasswd
```

To shell into the container:

```
docker exec -it qtumd_regtest sh
```

Once inside the container, use `qtum-cli` to interact with qtumd, for example:

```
qtum-cli -rpcuser=qtum -rpcpassword=testpasswd getblockchaininfo
```

For a list of available `qtum-cli` commands and arguments:

```
qtum-cli -rpcuser=qtum -rpcpassword=testpasswd help
```

```
... more
listsinceblock ( "blockhash" target_confirmations include_watchonly)
listtransactions ( "account" count skip include_watchonly)
listunspent ( minconf maxconf  ["addresses",...] [include_unsafe] )
lockunspent unlock ([{"txid":"txid","vout":n},...])
move "fromaccount" "toaccount" amount ( minconf "comment" )
removeprunedfunds "txid"
reservebalance [<reserve> [amount]]
sendfrom "fromaccount" "toaddress" amount ( minconf "comment" "comment_to" )
sendmany "fromaccount" {"address":amount,...} ( minconf "comment" ["address",...] )
sendmanywithdupes "fromaccount" {"address":amount,...} ( minconf "comment" ["address",...] )
sendtoaddress "address" amount ( "comment" "comment_to" subtractfeefromamount )
sendtocontract "contractaddress" "data" (amount gaslimit gasprice senderaddress broadcast)
setaccount "address" "account"
settxfee amount
signmessage "address" "message"
```

For online documentation for available rpc calls: [Qtum RPC API · Qtum Documentation](https://docs.qtum.site/en/Qtum-RPC-API/)
