---
layout: post
title:  "Atomic Swaps Across Bitcoin Chain Splits"
date:   2017-10-11 22:00:00 -0400
---
# Atomic Swaps Across Bitcoin Chain Splits

There has been a bit of interest regarding a way to do atomic swaps between two blockchains which will split from each other with a hard fork. This post describes a way to do so with neither party needing to trust each other or a third party. This post is written in the context of the Segwit2x hard fork (and an example will be given using that) but can be applied to any such hard forks.

## Requirements

First, let's establish what we want to do

 - Allow two parties, Alice and Bob, to swap their coins in the future at or after the time of the hard fork without trusting each other a third party
 - Allow both parties to back out of the agreement at any time and neither lose coins.
 
## The Method

### The P2SH address

To do this, we need to first create something that can only be spent at the time of the fork. We will construct a 2-of-2 multisig address that also makes use of `OP_CHECKLOCKTIMEVERIFY` to do so. This allows us to require both parties to sign the transaction so no coins can be stolen, and allows the spending transaction to only be confirmed after the fork has occurred. Our redeem script will be like so:

    <fork activation height/time> OP_CHECKLOCKTIMEVERIFY OP_DROP OP_2 <Alice's pubkey> <Bob's pubkey> OP_2 OP_CHECKMULTISIG

Now that this address has been created, both Alice and Bob will fund the address with the coins they wish to swap.

### The Spending Transactions

The first transaction will send the entirety of the coins in the above address to Alice on chain A. To do this, we simply create a transaction which sends the coins to an Alice's address and include the replay protection that is employed by chain A (e.g. a specific output that chain B will reject). This transaction must have a `nLocktime` value that is greater than `<fork activation height/time>` (it must be the same type). We will actually set this to `<fork activation height/time> + 2 days` to allow for the backing out transaction. Alice should have a copy of this transaction, and Bob can as well.

The second transaction is very similar to the first transaction. It will send the entirety of the coins in the above address to Bob on chain B. To do this, we create a transaction which sends the coins to Bob's address and include the replay protection that is employed by chain B (e.g. a new parameter that makes this transaction invalid on chain A). This transaction must also have a nLocktime value that is greater than `<fork activation height/time>` (it must be the same type). We will actually set this to `<fork activation height/time> + 2 days` to allow for the backing out transaction. Bob should have a copy of this transaction, and Alice can as well.

### Backing out

If Alice or Bob decides that they really don't want to go through with the swap, they have a way to back out after the fork activation height. To back out, Alice and Bob create a third transaction which sends the amount that they put in (minus transaction fees) back to themselves on their respective addresses. This transaction has an nLocktime value of `<fork activation height/time>`. If Alice or Bob wants to back out of the atomic swap, after the fork activation height/time has passed, this back out transaction can be broadcast and once it confirms, the prior two transactions will be invalidated. Both Alice and Bob should have a copy of this transaction.

### Fork Day Events

When the hard fork activates, if Alice or Bob wishes to back out, they should broadcast the back out transaction immediately. Because it was done in advance, the transaction fees may not be enough and a Child-Pays-For-Parent transaction may be needed. It will have two days to confirm.

If Alice and Bob wish to continue with the swap, then they will wait until 2 days after the fork occurs and broadcast their respective transactions. Alice will broadcast here transaction to the network using chain A and Bob will broadcast his transaction to the network using chain B. Because both transactions employ replay protection, they are not at risk of being replayed on either network and thus losing coins.

## An Example with Segwit2x

Now we will have an example atomic swap with Segwit2x. The examples will be done with Bitcoin Core with a patch applied so that it can sign for the special P2SH address. 

Suppose Alice wishes to swap 10 B2X for 10 of Bob's BTC. This means that after the swap is complete, Alice will have 20 BTC and Bob will have 20 B2X.

First they will create their addresses. Alice generates a new address and retrieves its public key:

    $ bitcoin-cli getnewaddress
    1Hs12LwwdXu2bhiuqyPfmWqUmTcZSBqEiS
    $ bitcoin-cli validateaddress 1Hs12LwwdXu2bhiuqyPfmWqUmTcZSBqEiS
    {
      "isvalid": true,
      "address": "1Hs12LwwdXu2bhiuqyPfmWqUmTcZSBqEiS",
      "scriptPubKey": "76a914b8f6d4790d053d6b781f5fe229ecab8aab881d4388ac",
      "ismine": true,
      "iswatchonly": false,
      "isscript": false,
      "iswitness": false,
      "pubkey": "02fd4dbc0f5076881586ce7ae84a3bd2854d2c2ca7782e7d3de9d4cfe64c20daa9",
      "iscompressed": true,
      "account": "",
      "timestamp": 1505671741,
      "hdkeypath": "m/0'/0'/3'",
      "hdmasterkeyid": "5daeb23c6fda331386be884baab609d78531292e"
    }

And Bob generates a new address and retrieves its public key:

    $ bitcoin-cli getnewaddress
    1JngAG1EfbjLqcfe3qcHNeV4Aeqc3NK4v3
    $ bitcoin-cli validateaddress 1JngAG1EfbjLqcfe3qcHNeV4Aeqc3NK4v3
    {
      "isvalid": true,
      "address": "1JngAG1EfbjLqcfe3qcHNeV4Aeqc3NK4v3",
      "scriptPubKey": "76a914c31d8ba0a258f80eabade40a705f5831c8d28d6488ac",
      "ismine": true,
      "iswatchonly": false,
      "isscript": false,
      "iswitness": false,
      "pubkey": "0208fe052d79b9feeb2d5b12ba72d5fab857a055275d1c550573c61c251751a643",
      "iscompressed": true,
      "account": "",
      "timestamp": 1505671741,
      "hdkeypath": "m/0'/0'/4'",
      "hdmasterkeyid": "5daeb23c6fda331386be884baab609d78531292e"
    }

Now they are going to create the redeem script for their P2SH address. This will first be done with `bitcoin-cli` and then modified by hand:

    $ bitcoin-cli createmultisig 2 '["02fd4dbc0f5076881586ce7ae84a3bd2854d2c2ca7782e7d3de9d4cfe64c20daa9","0208fe052d79b9feeb2d5b12ba72d5fab857a055275d1c550573c61c251751a643"]'
    {
      "address": "34sQK7HNe7eXwhduxcCNTxXaRJFyBWH6K4",
      "redeemScript": "522102fd4dbc0f5076881586ce7ae84a3bd2854d2c2ca7782e7d3de9d4cfe64c20daa9210208fe052d79b9feeb2d5b12ba72d5fab857a055275d1c550573c61c251751a64352ae"
    }

We will need to prepend `494785 OP_CHECKLOCKTIMEVERIFY OP_DROP` (`0x03c18c07b175`) to the redeem script to get the redeem script that we want:

    03c18c07b175522102fd4dbc0f5076881586ce7ae84a3bd2854d2c2ca7782e7d3de9d4cfe64c20daa9210208fe052d79b9feeb2d5b12ba72d5fab857a055275d1c550573c61c251751a64352ae

Then to make sure everything was done correctly and to get the address, we do the following:

    $ bitcoin-cli decodescript 03c18c07b175522102fd4dbc0f5076881586ce7ae84a3bd2854d2c2ca7782e7d3de9d4cfe64c20daa9210208fe052d79b9feeb2d5b12ba72d5fab857a055275d1c550573c61c251751a64352ae
    {
      "asm": "494785 OP_CHECKLOCKTIMEVERIFY OP_DROP 2 02fd4dbc0f5076881586ce7ae84a3bd2854d2c2ca7782e7d3de9d4cfe64c20daa9 0208fe052d79b9feeb2d5b12ba72d5fab857a055275d1c550573c61c251751a643 2 OP_CHECKMULTISIG",
      "type": "nonstandard",
      "p2sh": "33HDZJFYCyREPtRmNDWuTAedqToU9TptYW"
    }

Now Alice and Bob are going to fund that address with transactions `$TX_A` and `$TX_B`. These have outputs `$VOUT_A` and `$VOUT_B` which fund the above address.

We now need to create our spending transactions. First we create Alice's spending transaction. The replay protection that we employ here is an OP_RETURN output with the string `RP=!>1x` as implemented in this [Pull Request to btc1](https://github.com/btc1/bitcoin/pull/134):

    $ bitcoin-cli createrawtransaction '[{"txid":"$TX_A","vout":$VOUT_A},{"txid":"$TX_B","vout":$VOUT_B}]' '{"1Hs12LwwdXu2bhiuqyPfmWqUmTcZSBqEiS":19.999,"data":"52503d213e3178"}' 495072
    0200000002..e08d0700

Next we create Bob's spending transaction. The replay protection that we employ here is that the transaction will be larger than 999920 bytes and less than 1000000 bytes so that it is too large to fit in a Bitcoin block but just small enough to fit in a Segwit2x block. To do so, we create 1886 outputs which have a maximum script size of 520 bytes and 1 output of 400 bytes. Note that we actually give it 516 bytes of data for the 1881 outputs and 394 bytes of data for the last output because three bytes are used to represent the size of the data and one byte for the OP_RETURN. Unfortunately this transaction is going to be nonstandard, but it is the only known way to make a transaction only valid on the Segwit2x chain without using UTXO tainting.

    $ bitcoin-cli createrawtransaction '[{"txid":"$TX_A","vout":$VOUT_A},{"txid":"$TX_B","vout":$VOUT_B}]' '{"1JngAG1EfbjLqcfe3qcHNeV4Aeqc3NK4v3":19.999,"data":"<516 bytes of data>",<repeat 1886 times>,"data":"<396 bytes of data>"}' 495072
    0200000002..e08d0700

And then we create our back out transaction:

    $ bitcoin-cli createrawtransaction '[{"txid":"$TX_A","vout":$VOUT_A},{"txid":"$TX_B","vout":$VOUT_B}]' {"1JngAG1EfbjLqcfe3qcHNeV4Aeqc3NK4v3":9.999,"1Hs12LwwdXu2bhiuqyPfmWqUmTcZSBqEiS":9.999}' 494785
    0200000002..c18c0700

Lastly we sign these transactions. To sign, we will need to provide the previous transaction outputs that are being spent so that we can provide the redeem script. Because we need to provide the redeem script, we also need to provide the private keys. The scriptPubKey will be denoted as $SPK (it is the same for both outputs being spent). The private keys will be deonoted as $PRK_A and $PRK_B:

    $ bitcoin-cli signrawtransaction 0200000002..e08d0700 '[{"txid":"$TX_A","vout":$VOUT_A,"scriptPubKey":"$SPK","redeemScript":"03c18c07b175522102fd4dbc0f5076881586ce7ae84a3bd2854d2c2ca7782e7d3de9d4cfe64c20daa9210208fe052d79b9feeb2d5b12ba72d5fab857a055275d1c550573c61c251751a64352ae"},{"txid":"$TX_B","vout":$VOUT_B,"scriptPubKey":"$SPK","redeemScript":"03c18c07b175522102fd4dbc0f5076881586ce7ae84a3bd2854d2c2ca7782e7d3de9d4cfe64c20daa9210208fe052d79b9feeb2d5b12ba72d5fab857a055275d1c550573c61c251751a64352ae"}]' '["$PRK_A","$PRK_B"]'
    0200000002..e08d0700
    $ bitcoin-cli signrawtransaction 0200000002..e08d0700 '[{"txid":"$TX_A","vout":$VOUT_A,"scriptPubKey":"$SPK","redeemScript":"03c18c07b175522102fd4dbc0f5076881586ce7ae84a3bd2854d2c2ca7782e7d3de9d4cfe64c20daa9210208fe052d79b9feeb2d5b12ba72d5fab857a055275d1c550573c61c251751a64352ae"},{"txid":"$TX_B","vout":$VOUT_B,"scriptPubKey":"$SPK","redeemScript":"03c18c07b175522102fd4dbc0f5076881586ce7ae84a3bd2854d2c2ca7782e7d3de9d4cfe64c20daa9210208fe052d79b9feeb2d5b12ba72d5fab857a055275d1c550573c61c251751a64352ae"}]' '["$PRK_A","$PRK_B"]'
    0200000002..e08d0700
    $ bitcoin-cli signrawtransaction 0200000002..c18c0700 '[{"txid":"$TX_A","vout":$VOUT_A,"scriptPubKey":"$SPK","redeemScript":"03c18c07b175522102fd4dbc0f5076881586ce7ae84a3bd2854d2c2ca7782e7d3de9d4cfe64c20daa9210208fe052d79b9feeb2d5b12ba72d5fab857a055275d1c550573c61c251751a64352ae"},{"txid":"$TX_B","vout":$VOUT_B,"scriptPubKey":"$SPK","redeemScript":"03c18c07b175522102fd4dbc0f5076881586ce7ae84a3bd2854d2c2ca7782e7d3de9d4cfe64c20daa9210208fe052d79b9feeb2d5b12ba72d5fab857a055275d1c550573c61c251751a64352ae"}]' '["$PRK_A","$PRK_B"]'
    0200000002..c18c0700

And broadcast them:

    $ bitcoin-cli sendrawtransaction 0200000002..e08d0700
    {TXID}
    $ bitcoin-cli sendrawtransaction 0200000002..e08d0700
    {TXID}
    $ bitcoin-cli sendrawtransaction 0200000002..c18c0700
    {TXID}

### Software For This Example

Some scripts and code changes that are necessary for this example can be found below:

 - [Bash Script to create Bob's spending transaction](https://gist.github.com/achow101/b1cd6155f96f72464a6a51439108ed1a)
 - [Branch with the signing patch](https://github.com/achow101/bitcoin/tree/sign-cltv)
 - [Signing patch itself](https://gist.github.com/achow101/86a4fdcb3cebc1a57968715f34d66e0a)
 