# Paying for other user's fees

Qtum [QIP-5](https://blog.qtum.org/qip-5-add-op-sender-opcode-571511802938) added a custom bitcoin script opcode `OP_SENDER`

This opcode lets you pay for fees/gas with one account and do EVM transactions with another account - this account doesn't even need to have Qtum.

The bitcoin script for this type of transaction looks like [this](https://qtum.info/tx/51f7c3202a3d828a5b79bc840ff2b16e7c42804cb8a85e7c036474cccdb39b5a)

```
// Address type
// PUBKEYHASH = 1,
// SCRIPTHASH = 2,
// WITNESSSCRIPTHASH = 3,
// WITNESSPUBKEYHASH = 4,
// NONSTANDARD = 5
1
// Address
093c2ab5bd4c28e7d66e8e2745564126256e95a1
// {signature, pubkey} // serialized scriptSig
6a4730440220684046d36bd6960309d446050cbd48d629253db689ffd9423038f2d9aed0929002200728da6043a2195d80a080e99308f5657ce5a204d7b67af0b0249697422ca80c0121033b0f572752b89704201e50e3f7c1b9051e94b2cc5ff60e73ea085babdec9c19f
// opcode
OP_SENDER
// normal OP_CALL or OP_CREATE bitcoin script follows
// EVM Version
4
// gas limit
250000
// gas price in satoshi
40
[byte code] a9059cbb0000000000000000000000007dfa26095ae1fbd7992dbefd335f5b20250fd2bf0000000000000000000000000000000000000000000000106a1f56e1ed4b9000
54fefdb5b31164f66ddb68becd7bdd864cacd65b
OP_CALL
```

Qtumd and [Qtumj](https://github.com/qtumproject/qtumj/blob/master/examples/src/main/java/org/bitcoinj/examples/ContractOpSender.java) support OP_SENDER

The signature for OP_SENDER is the DER encoded signature of the Sha256 SegwitSignatureHash of the script sig followed by the sighash

* See [qtum_op_sender.py](https://github.com/qtumproject/qtum/blob/master/test/functional/qtum_op_sender.py) for the python implementation of how to generate the signature
