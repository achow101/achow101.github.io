---
layout: post
title:  "Clearing the FUD around Segwit"
date:   2016-04-01 15:00:00 -0500
---
# Clearing the FUD around Segregated Witness

Lately I have been noticing on a variety of online bitcoin communities such as [bitcointalk.org](https://bitcointalk.org), [/r/bitcoin](https://reddit.com/r/bitcoin), [/r/btc](https://reddit.com/r/btc), [bitco.in](https://bitco.in/forum/), and [bitcoin.com](https://forum.bitcoin.com) that many users do not understand what Segregated Witness (commonly known as segwit) is and what it does. I have seen that there are plenty of either uninformed or misinformed users spreading FUD (fear, uncertainty, doubt) about segwit. I am here to clear this up.

## What is SegWit?

Segregated Witness is a solution created by the Bitcoin Core developers. It is spearheaded by Pieter Wuille, Johnson Lau, and Eric Lombrozo. The general high-level idea of segwit is that the signatures in a transaction (also called the witness data) are skipped when calculating the transaction id. By doing so it solves many issues that Bitcoin has. These benefits are laid out [here](https://bitcoincore.org/en/2016/01/26/segwit-benefits/).

## Clearing up the myths

In this section, I will clear up many of the myths and misinformation that surround segwit. This will grow and change as people respond to me.

### Myth: Segwit is primarily for the Lightning Network

This is simply not true. The true and original purpose of segwit was to prevent transaction malleability. Since it removes the signatures from the data that is hashed to become the transaction id, there is then simply no way for a third party to change the signature data so that it was still valid but produced a different transaction id from the original. This attack is the High-S/Low-S attack which works because the signatures which protect the rest of the transaction from being modified cannot protect itself. With this separation, segwit is no longer vulnerable to these transaction malleability attacks as the txid is then calculated from data that cannot be changed at all by a third party.

Although it began as a transaction malleability fix, it has evolved to something a little more complex. Segwit introduces a new hash pre-image generation algorithm which will make signature hashing operations scale linearly. The hash pre-image is the data that is to be hashed. This hash will be signed and that is the signature for the transaction. The change enables the use of hash midstates which allows for faster and more efficient hashing. This makes the relationship between the number of signature hashes and the time to generate them linear instead of the former quadratic relationship.

Additionally, to maintain backwards compatibility, the size of the transaction that counts for part of the block size is the size of the data with the old transaction serialization. This means that the witness data is not included in this calculation. This means that a significant portion of the data in a transaction is not counted in the block size more transactions can then fit in a block. This is effectively a capacity increase. However full nodes will still be downloading all of the witness data to verify blocks and transactions and they will still have the same network bandwidth cost as an increased block size to support a similar capacity would have.

### Myth Segwit as a soft fork is more dangerous than a hard fork

Segwit deployed as a soft fork is actually safer than a hard fork. A soft fork means that backwards and forwards compatibility is maintained. Old versions of Bitcoin software will be able to function with no ill effect when a soft fork is deployed. In contrast, a hard fork requires that every single Bitcoin user upgrade their software to support the new consensus rules. This has the effect of being not backwards compatible and thus forcing users to upgrade to the latest version or risk being kicked off of the Bitcoin network.

Segwit was in fact originally planned as a hard fork, but the developers realized that it could be done as a soft fork and doing so would be safer than a hard fork. The only differences between segwit as a soft fork and as a hard fork is where the witness root hash goes and whether the witness data is counted in the block size or not. With the witness root hash, it could have either been in the block header or in the coinbase transaction. Segwit chose to put it in the coinbase transaction as an OP_RETURN output. This is safer because this allows for segwit to be done as a softfork even though the data is still the same size as it would have been if it were in the header. The other difference is whether to include the witness data in the block size count, and to maintain backwards compatibility while also having the capacity increase, it was decided to not do so.

### Myth: Segwit is kludgy hacked together software that is not ready.

No, this is very far from the truth. The Bitcoin Core developers have very strict testing and require that a test exist to test every single scenario. When new functionality is introduced, an automated test must also exist. The developers will also review the code and test it themselves to guarantee that it works as intended. Segwit is also written by some of very good developers who both came up with the concept and then implemented it. Before being added to Bitcoin Core, it will be tested very thoroughly over several days or even weeks to make sure that it is all ready. The implementation now is already being tested, with it being run by several people and on its fourth iteration of the segregated witness test network.

There are however, a few hacks which are designed to allow segwit nodes to maintain compatibility with older software. The witness root hash being placed in the Coinbase transaction and the witness data not being counted towards the block size are two most prominent hacks that segwit employs. These are necessary to maintain the compatibility with non-upgraded nodes.

### Myth: Segwit is much more complex than a super simple hard fork.

While Segwit is indeed more complex and introduces many changes, but it is a relatively simple conceptual change, similar to a hard fork to increase the blocksize limit. On the surface, they appear simple. Segwit ignores the signatures when calculating the transactions, but as a soft fork, some additional changes must be made to make segwit transactions compatible with non-segwit nodes. These changes then have side effects which can be beneficial to Bitcoin. It also contains more functionality than a hard fork increasing the block size limit. The hard fork to increase the blocksize limit also appears simple, but additional changes need to be made to support the deployment and to solve the quadratic hashing issue with transactions.

Additionally the BIPs and the Segwit Wallet Dev Guide are detailed and can help developers implement Segwit properly.

### Myth: When segwit activates, I won't be able to send or receive my Bitcoin anymore if I don't upgrade.

With the way that segwit is designed, people can still send their transactions using Pay-to-Pubkey-Hash, Pay-to-Script-Hash, and Pay-to-Pubkey outputs as the current software do. These outputs and they way to spend from these outputs will not change. The signatures for verifying these transactions will not be moved and will remain in the scriptsig.

What segwit does change is that it adds two new output types, Pay-to-Witness-Pubkey-Hash and Pay-To-Witness-Script-Hash. These two new output types require that the signatures to spend from those outputs are in a new Script Witness array which is not included in the hash of the transaction. The scriptsig of the inputs that spend those output types are empty. These output types are still compatible with non-upgraded nodes because they will always validate as true to those non-upgraded nodes because when they validate those transactions, the stack is not empty and not zero. Upgraded nodes will validate them fully because those nodes have access to the signatures and recognize that what the output format is.

Segwit also creates a new type of Pay-to-Script-Hash address. The redeemscript of the p2sh address is one of the two new witness output types. The redeemscript is hashed and that becomes a p2sh address. This allows for senders who have not yet upgraded to send to a person who has upgraded and the recipient, when he spends from this p2sh output, can take advantage of segwit's benefits.

Overall, the changes that segwit makes do maintain compatibility with non-upgraded nodes. With these changes, a non-upgraded user can send Bitcoin using the current output types, thus able to send to both upgraded and non-upgraded users. Because the transaction uses the current output types, the following transaction cannot take advantage of segwit. However, a non-upgraded user can send to a segwit output nested in a p2sh output which allows them to send to upgraded users who can then take advantage of segwit for the following transaction. Upgraded users can also spend from any of the output types and still create the current output types so they can send to non-upgraded users as well.

### Myth: If I don't upgrade, I can be attacked by people sending me transactions with segwit outputs

If you don't upgrade, your wallet will not know of the segwit outputs. It will not know that these outputs are meant for you. This can of course create problems if an upgraded user decides to create a segwit output when sending to a non-upgraded user. The non-upgraded user will not recognize the transaction is for him and this can create disputes. Unfortunately this flaw cannot be fixed through the consensus rules but rather through software implementation. For this, the best thing to do is for software to not use the segwit output types at all but rather continue to use the currently used p2pkh, p2sh, and p2pk output types. This can still take advantage of segwit because the new segwit output types can be nested inside of a p2sh address. New wallet addresses should be of this type; the p2wpkh or p2wsh output is placed as the redeemscript of a p2sh address. Thus users can still send to each other without issue and the upgraded users can still take advantage of segwit.

### Myth: Miners who don't upgrade to segwit will be forced off of the network

This is not true, miners will not be forked off of the network. Segwit will be deployed using BIP9 versionbits which uses a 95% threshold. If a miner had less than 5% of the hash power and did not upgrade, he would not run into significant trouble so long as he follows certain rules (the standardness rules) which most miners do actually follow today. If this miner follows the standardness rules of today which are widely accepted by everyone, then he does not risk being forked. His blocks would simply not contain an transactions that spend from the output types nor have transactions that create the new output types. His blocks do not need to have the witness root hash in the coinbase so long as the block does not contain any transactions that have witnesses.

However, if the miner did not follow the standardness rules, then he could end up including transactions with witnesses but he wouldn't have the witnesses nor would he have the witness root hash in the coinbase. This would be an invalid block and it would be orphaned. Because the vast majority of the hash power supports segwit, this miner's small fork would end up orphaned and he would switch back to the longest blockchain which was extended on by the segwit miners.

Additionally, the miner has an incentive to upgrade as less and less transactions will continue to be the current normal transactions. More and more will have the segwit outputs which and spend from them which reduces the fees that this miner could get. Eventually, there will be some point where there are no transactions that this miner could include a block and thus he has an incentive to upgrade in order to get the transaction fees. If he did not, he would only earn from the block subsidy.

### Myth: The witness discounts are to incentivise people to use segwit

The primary idea behind the discounts are to incentivise wallets to manage change differently and clean up the UTXO set. This is best explained by Adam Back [here](https://www.reddit.com/r/Bitcoin/comments/4d3pdg/clearing_the_fud_around_segwit/d1nxn28)

## See also
 - [BIP 141: Segregated Witness (Consensus Layer)](https://github.com/bitcoin/bips/blob/master/bip-0141.mediawiki)
 - [BIP 143: Transaction Signature Verification for Version 0 Witness Program](https://github.com/bitcoin/bips/blob/master/bip-0143.mediawiki)
 - [BIP 144: Segregated Witness (Peer Services)](https://github.com/bitcoin/bips/blob/master/bip-0144.mediawiki)
 - [Segregated Witness Wallet Development Guide](https://bitcoincore.org/en/segwit_wallet_dev/)
 - [Segregated Witness Benefits](https://bitcoincore.org/en/2016/01/26/segwit-benefits/)
