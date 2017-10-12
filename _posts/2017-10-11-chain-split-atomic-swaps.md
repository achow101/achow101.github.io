---
layout: post
title:  "Atomic Swaps Across Bitcoin Chain Splits"
date:   2017-10-11 22:00:00 -0400
---
# Atomic Swaps Across Bitcoin Chain Splits

## Updates

 - A previous version of this suggested the use of `OP_CHECKLOCKTIMEVERIFY` but after futher thought, I do not believe that is necessary.
 
 ***

There has been a bit of interest regarding a way to do atomic swaps between two blockchains which will split from each other with a hard fork. This post describes a way to do so with neither party needing to trust each other or a third party. This post is written in the context of the Segwit2x hard fork (and an example will be given using that) but can be applied to any such hard forks.

## Requirements

First, let's establish what we want to do

 - Allow two parties, Alice and Bob, to swap their coins in the future at or after the time of the hard fork without trusting each other a third party
 - Allow both parties to back out of the agreement at any time and neither lose coins.
 
## The Method

### The Multisig Address

To do this, we need both parties to approve the transaction, so we use a simple 2-of-2 mulltisig address. This way the transactions that pay from the address must be signed by both parties. Once we create the address, both Alice and Bob will fund the address with the coins they wish to swap.

### The Spending Transactions

The first transaction will send the entirety of the coins in the above address to Alice on chain A. To do this, we simply create a transaction which sends the coins to an Alice's address and include the replay protection that is employed by chain A (e.g. a specific output that chain B will reject). We then set the `nLocktime` value of the transaction to be that of the fork activation height or time.  We will actually set this to `<fork activation height/time> + 2 days` to allow for the backing out transaction. Alice should have a copy of this transaction, and Bob can as well.

The second transaction is very similar to the first transaction. It will send the entirety of the coins in the above address to Bob on chain B. To do this, we create a transaction which sends the coins to Bob's address and include the replay protection that is employed by chain B (e.g. a new parameter that makes this transaction invalid on chain A). We then set the `nLocktime` value of the transaction to be that of the fork activation height or time.  We will actually set this to `<fork activation height/time> + 2 days` to allow for the backing out transaction. Bob should have a copy of this transaction, and Alice can as well.

### Backing out

If Alice or Bob decides that they really don't want to go through with the swap, they have a way to back out after the fork activation height. To back out, Alice and Bob create a third transaction which sends the amount that they put in (minus transaction fees) back to themselves on their respective addresses. This transaction does not have a `nLocktime` value at all so it can be broadcast at any time. If Alice or Bob wants to back out of the atomic swap at any time, this back out transaction can be broadcast and once it confirms, the prior two transactions will be invalidated. Both Alice and Bob should have a copy of this transaction.

### Fork Day Events

When the hard fork activates, if Alice or Bob wishes to back out, they should broadcast the back out transaction immediately. Because it was done in advance, the transaction fees may not be enough and a Child-Pays-For-Parent transaction may be needed. It will have two days to confirm.

If Alice and Bob wish to continue with the swap, then they will wait until 2 days after the fork occurs and broadcast their respective transactions. Alice will broadcast here transaction to the network using chain A and Bob will broadcast his transaction to the network using chain B. Because both transactions employ replay protection, they are not at risk of being replayed on either network and thus losing coins.

## An Example with Segwit2x

Now we will have an example atomic swap with Segwit2x. The examples will be done with Bitcoin Core.

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

Now they are going to create the multisig address:

    $ bitcoin-cli addmultisigaddress 2 '["02fd4dbc0f5076881586ce7ae84a3bd2854d2c2ca7782e7d3de9d4cfe64c20daa9","0208fe052d79b9feeb2d5b12ba72d5fab857a055275d1c550573c61c251751a643"]'
    34sQK7HNe7eXwhduxcCNTxXaRJFyBWH6K4

Now Alice and Bob are going to fund that address with transactions `$TX_A` and `$TX_B`. These have outputs `$VOUT_A` and `$VOUT_B` which fund the above address.

We now need to create our spending transactions. First we create Alice's spending transaction. The replay protection that we employ here is an OP_RETURN output with the string `RP=!>1x` as implemented in this [Pull Request to btc1](https://github.com/btc1/bitcoin/pull/134):

    $ bitcoin-cli createrawtransaction '[{"txid":"$TX_A","vout":$VOUT_A},{"txid":"$TX_B","vout":$VOUT_B}]' '{"1Hs12LwwdXu2bhiuqyPfmWqUmTcZSBqEiS":19.999,"data":"52503d213e3178"}' 495072
    0200000002..e08d0700

Next we create Bob's spending transaction. The replay protection that we employ here is that the transaction will be larger than 999920 bytes and less than 1000000 bytes so that it is too large to fit in a Bitcoin block but just small enough to fit in a Segwit2x block. To do so, we create 1886 outputs which have a maximum script size of 520 bytes. Note that we actually give it 516 bytes of data for the 1882 outputs because three bytes are used to represent the size of the data and one byte for the OP_RETURN. Unfortunately this transaction is going to be nonstandard, but it is the only known way to make a transaction only valid on the Segwit2x chain without using UTXO tainting.

    $ bitcoin-cli createrawtransaction '[{"txid":"$TX_A","vout":$VOUT_A},{"txid":"$TX_B","vout":$VOUT_B}]' '{"1JngAG1EfbjLqcfe3qcHNeV4Aeqc3NK4v3":19.999,"data":"<516 bytes of data>",<repeat 1882 times>}' 495072
    0200000002..e08d0700

And then we create our back out transaction:

    $ bitcoin-cli createrawtransaction '[{"txid":"$TX_A","vout":$VOUT_A},{"txid":"$TX_B","vout":$VOUT_B}]' {"1JngAG1EfbjLqcfe3qcHNeV4Aeqc3NK4v3":9.999,"1Hs12LwwdXu2bhiuqyPfmWqUmTcZSBqEiS":9.999}' 494785
    0200000002..c18c0700

Lastly we sign these transactions:

    $ bitcoin-cli signrawtransaction 0200000002..e08d0700
    0200000002..e08d0700
    $ bitcoin-cli signrawtransaction 0200000002..e08d0700
    0200000002..e08d0700
    $ bitcoin-cli signrawtransaction 0200000002..c18c0700
    0200000002..c18c0700

And broadcast them:

    $ bitcoin-cli sendrawtransaction 0200000002..e08d0700
    {TXID}
    $ bitcoin-cli sendrawtransaction 0200000002..e08d0700
    {TXID}
    $ bitcoin-cli sendrawtransaction 0200000002..c18c0700
    {TXID}

### Software For This Example

 - [Bash Script to create Bob's spending transaction](https://gist.github.com/achow101/b1cd6155f96f72464a6a51439108ed1a)
 
## Acknowledgements

I think Greg Maxwell first posted about this method on Reddit but did not go into as much detail or with an example as I have done here. I cannot find the post for it though.
 