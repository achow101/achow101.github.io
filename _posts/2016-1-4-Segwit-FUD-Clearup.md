---
layout: post
title:  "Clearing the FUD around Segwit"
date:   2016-04-01 15:00:00 -0500
---
# Clearing the FUD around Segregated Witness

Lately I have been noticing on a variety of online bitcoin communities such as [bitcointalk.org](https://bitcointalk.org), [/r/bitcoin](https://reddit.com/r/bitcoin), [/r/btc](https://reddit.com/r/btc), [bitco.in](https://bitco.in/forum/), and [bitcoin.com](https://forum.bitcoin.com) that many users do not understand what Segregated Witness (commonly known as segwit) is and what it does. I have seen that there are plenty of either uninformed or misinformed users spreading FUD (fear, uncertainty, doubt) about segwit. I am here to clear this up.

## What is SegWit?

Segregated Witness is a solution created by the Bitcoin Core developers. It is spearheaded by Pieter Wuille, Johnson Lau, and Eric Lombrozo. The general high-level idea of segwit is that the signatures in a transaction (also called the witness data) is removed from the transaction and put in another place (hence segregated witness). By separating this data from the rest of the transaction, it solves many issues that Bitcoin has. These benefits are laid out [here](https://bitcoincore.org/en/2016/01/26/segwit-benefits/).

## Clearing up the myths

In this section, I will clear up many of the myths and misinformation that surround segwit. This will grow and change as people respond to me.

### Myth: Segwit is primarily for the Lightning Network

This is simply not true. The true and original purpose of segwit was to prevent transaction malleability. Since it removes the signatures from the data that is hashed to become the transaction id, there is then simply no way for a third party to change the signature data so that it was still valid but produced a different transaction id from the original. This attack is the High-S/Low-S attack which works because the signatures which protect the rest of the transaction from being modified cannot protect itself. With this separation, segwit is no longer vulnerable to these transaction malleability attacks as the txid is then calculated from data that cannot be changed at all by a third party.

Although it began as a transaction malleability fix, it has evolved to something a little more complex. Segwit introduces a new hash pre-image generation algorithm which will make signature hashing operations scale linearly. The hash pre-image is the data that is to be hashed. This hash will be signed and that is the signature for the transaction. The change enables the use of hash midstates which allows for faster and more efficient hashing. This makes the relationship between the number of signature hashes and the time to generate them linear instead of the former quadratic relationship.

An additional side effect of segwit is that the witness data is not counted as data that goes into a block. This change is to maintain backwards compatibility. This means that a significant portion of the data in a transaction is not counted in the block size more transactions can then fit in a block. This is effectively a capacity increase. However full nodes will still be downloading all of the witness data to verify blocks and transactions and they will still have the same network bandwidth cost as an increased block size to support a similar capacity would have.

### Myth Segwit as a soft fork is more dangerous than a hard fork

Segwit deployed as a soft fork is actually safer than a hard fork. A soft fork means that backwards compatibility is maintained. Old versions of Bitcoin software will be able to function with no ill effect when a soft fork is deployed. In contrast, a hard fork requires that every single Bitcoin user upgrade their software to support the new consensus rules. This has the effect of being not backwards compatible and thus forcing users to upgrade to the latest version or risk being kicked off of the Bitcoin network.

Segwit was in fact originally planned as a hard fork, but the developers realized that it could be done as a soft fork and doing so would be safer than a hard fork. The only differences between segwit as a soft fork and as a hard fork is where the witness root hash goes and whether the witness data is counted in the block size or not. With the witness root hash, it could have either been in the block header or in the coinbase transaction. Segwit chose to put it in the coinbase transaction as an OP_RETURN output. This is safer because this allows for segwit to be done as a softfork even though the data is still the same size as it would have been if it were in the header. The other difference is whether to include the witness data in the block size count, and to maintain backwards compatibility while also having the capacity increase, it was decided to not do so.

### Myth: Segwit is kludgy hacked together software that is not ready.

No, this is very far from the truth. The Bitcoin Core developers have very strict testing and require that a test exist to test every single scenario. When new functionality is introduced, an automated test must also exist. The developers will also review the code and test it themselves to guarantee that it works as intended. Segwit is also written by some of very good developers who both came up with the concept and then implemented it. Before being added to Bitcoin Core, it will be tested very thoroughly over several days or even weeks to make sure that it is all ready. The implementation now is already being tested, with it being run by several people and on its fourth iteration of the segregated witness test network.

### Myth: Segwit is much more complex than a super simple hard fork.

While Segwit is complex and introduces many changes, it is still about the same number of lines of code as the Bitcoin Classic implementation of the 2 Mb hard fork because that implementation still needs additional changes to mitigate the problems with quadratic hashing. However segwit packs more functionality than a 2 Mb hard fork. 

Additionally the BIPs and the Segwit Wallet Dev Guide are detailed and can help developers implement Segwit properly.

## See also
 - [BIP 141: Segregated Witness (Consensus Layer)](https://github.com/bitcoin/bips/blob/master/bip-0141.mediawiki)
 - [BIP 143: Transaction Signature Verification for Version 0 Witness Program](https://github.com/bitcoin/bips/blob/master/bip-0143.mediawiki)
 - [BIP 144: Segregated Witness (Peer Services)](https://github.com/bitcoin/bips/blob/master/bip-0144.mediawiki)
 - [Segregated Witness Wallet Development Guide](https://bitcoincore.org/en/segwit_wallet_dev/)
 - [Segregated Witness Benefits](https://bitcoincore.org/en/2016/01/26/segwit-benefits/)
