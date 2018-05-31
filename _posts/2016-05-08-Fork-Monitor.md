---
layout: post
title:  "Announcing the Bitcoin Fork Monitor"
date:   2016-05-08 9:00:00 -0500
---

# Updates

July 18th 2017: The site has been revived and rewritten in python and django in anticipation of the BIP 91 and BIP 148 forks.

June 19th 2016: The site has been shut down due to the expense of maintaining it.

# The Bitcoin Fork Monitor

I have created a new website, http://btcforkmonitor.info, which monitors the blockchain for the status of planned forks. It will continuously be updated as new forks are planned and deployed. It will only support forks that are assigned a BIP number and supported by a significant proportion of the Bitconi community. The site automatically follows and updates as new blocks are added and it will determine whether the block is supporting a specific fork.

## How it works

The website uses a bitcoind in the background to receive, validate, and relay blocks to the actual site process. The site's servlet backend will receive the block and examine the raw block data to determine which fork, if any, that the block supports.

## The source code

This website is open source. The source code can be found at https://github.com/achow101/ForkMonitor. It is written in Java and utilizes the [Google Web Toolkit](gwtproject.org), [ObjectDB](objectdb.com), and [ZeroMQ](zeromq.org) libraries. The site and the source code is licensed under the Affero Gneral Public License.

## See also

 - [Project Discussion on Bitcointalk](https://bitcointalk.org/index.php?topic=1458929.0)
 - [Service Announcement on Bitcointalk](https://bitcointalk.org/index.php?topic=1456884.0)