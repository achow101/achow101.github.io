---
layout: post
title:  "Bitcoin Core Troubleshooting FAQ and Tips"
date:   2016-07-24 22:00:00 -0500
---
# Bitcoin Core Troubleshooting FAQ and Tips

Over the past year, I have been hanging out on various Bitcoin forums, the most frequented being Bitcointalk, and helping people out with various tech support issues with Bitcoin Core. I decided to compile the most frequent issues and troubleshooting tips into this post here, partially to help people troubleshoot their install, and partially to help me not have to keep posting the same thing over and over again.

This document will continue to grow as I hear of more and more troubleshooting issues.

* [Troubleshooting FAQ](#faq)
* [Troubleshooting Tips](#tips)

## <a name="faq"></a>Frequently Asked Questions

* [Stuck Transaction](#stuck-tx)
* [Wallet Balance is Wrong](#wrong-bal)
* [Wallet is empty](#empty-wallet)

### <a name="stuck-tx"></a>A transaction is stuck, how do I make it confirm?

In Bitcoin Core, it is fairly easy to remove a transaction from your wallet so that you can resend the transaction with a higher fee. There are two methods to do so, the `abandontransaction` command and the `-zapwallettxes` startup option.

To use the `abandontransaction` RPC, you first must be running Bitcoin Core 0.12 or later. Then open up the [debug console](#debug-console) and use the command

    abandontransaction <txid>

where `<txid>` is the transaction id of your stuck transaction. Do this for every stuck transaction. If the command is successful, there will be no error and no output.

If `abandontransaction` did not work or if you do not have Bitcoin Core 0.12 or later, then you can use the `-zapwallettxes` startup option. To do so, just follow the instrucionts to [start Bitcoin Core with an option](#option-startup) where the option you want to use is `-zapwallettxes`.

### <a name="wrong-bal"></a>Wallet Balance is incorrect

If your wallet balance is incorrect, a number of things could have gone wrong. First double check that you are fully synced. Check a block explorer to see the latest block height. Next open Bitcoin Core and hover your mouse over the check mark at the bottom right hand corner. It should pop up with a little info box that says how many blocks Bitcoin Core has processed. This number should match the latest block height in the block explorer. If it does not, wait for Bitcoin Core to finish syncing. If you are synced, try [starting Bitcoin Core with the `-rescan` option](#option-startup). If that does not fix the problem, next check your receiving addresses at File > Receiving addresses. Make sure that you are not missing any addresses. If you are, [check whether the addresses you are missing are still in your wallet](#address-in-wallet). If the address is missing from your wallet, then you will have to restore from a recent backup in order to recover it. Otherwise there is nothing that can be done.

### <a name="empty-wallet"></a>Wallet is empty

If your wallet is empty and you see no familiar transactions or addresses, it is likely that the wallet file Bitcoin Core is currently using is not your actual wallet. If you had recently pressed the Reset Options button in Settings > Options, then your data directory may have been reverted to the default. To check, go to Help > Debug Window and go to the Information tab. In the field labeled Datadir, the path to your data directory will be listed. If that is not what you expect (custom if it was set, or default if not), then this is why your wallet is empty. If you had a custom data directory, you must [start Bitcoin Core with the `-datadir=<path>` option](#option-startup) where `<path>` is the path to your data directory.

## <a name="tips"></a>Troubleshooting tips

* [Using the Debug Console](#debug-console)
* [Start Bitcoin Core with options](#option-startup)
* [Check for a private key in the wallet](#address-in-wallet)
* [Open the debug.log file](#debug-log)

### <a name="debug-console"></a>Enter commands into the debug console

To open the debug console, go to Help > Debug Window in Bitcoin Core. In the new window that pops up, click on the Console tab. This is the debug console. You type the commands in the small box at the bottom of the window. The command `help` will give you a list of all commands available. Typing `help <command name>`, where `<command name>` is one of the commands, will give you the usage for that command.

The debug window will let you know if something was successful through the output of each command. Most of the commands will have an output. If the output is in black text, then your command was successfully run. If the output is in red text and has some sort of error code, then the command failed and there was an error.

The commands also take arguments in JSON format, so entering the commands with proper JSON format and escaping is essential.

### <a name="option-startup"></a>Start Bitcoin Core with a startup option

Starting Bitcoin Core with startup options can be very useful. The method of doing so is dependent on your Operating System

* [Windows](#windows-startup)
* [Linux](#linux-startup)
* [Mac OSX](#mac-startup)

#### <a name="windows-startup"></a>Windows

Right click the shortcut that you use for starting Bitcoin Core. Click on Properties in that menu. In the Properties window, go to the box labeled Target. Click the box and move your cursor all the way to the right, past what is already in there. Then just type the options you want, making sure that there is a space between what is already in the box and your option, and a space between each option. Then just click OK and double click the shortcut to start Bitcoin Core. When Bitcoin Core is fully started, you can repeat this process and remove the options that you added.

#### <a name="linux-startup"></a>Linux

Open the terminal in Linux. If you did not install Bitcoin Core and are just running the binary, navigate to the directory you have the bitcoin-qt file in. Then type

    bitcoin-qt <options>

where `<options>` are your startup options. Just make sure to have a space between each option. Press enter and Bitcoin Core will start with those options.

#### <a name="mac-startup"></a>Mac OSX

Disclaimer: I don't have a Mac so I am not 100% sure that this works, but it should.

Open the Mac terminal. Then type

    open Bitcoin-Qt.app --args <options>

where `<options>` are your startup options. Just make sure to have a space between each option. Press enter and Bitcoin Core will start with those options.

### <a name="address-in-wallet"></a>Check if the private key to an address is in the wallet

[Open the debug console](#debug-console) and use the command

    dumpprivkey <address>

where `<address>` is the Bitcoin address whose private key you want to check is in the wallet. If the command was successful, it will print the private key to the console. DO NOT SHARE THE PRIVATE KEY WITH ANYONE. This key should begin with a '5', 'K', or 'L'. If it does not, it is not a private key. If the command failed with an error, the private key to the address is not in your wallet.

### <a name="debug-log"></a>Get the debug.log file

The debug.log file is a log file that is very useful for troubleshooting. It does not leak any information about your private keys so your Bitcoin is always safe. To get the debug.log file, open Bitcoin Core and go to Help > Debug Window and go to the Information tab. Near the bottom right hand corner of the window is a button labeled Open with Debug log file. Click this to access the file. Now you can save the file elsewhere or copy the contents to send to someone.

If Bitcoin Core is unable to open for some reason, go to the data directory. If you set a custom data directory on first startup or with the `--datadir` option, then you must go there. The default locations are described at https://en.bitcoin.it/wiki/Data_directory#Default_Location. Once you are at the data directory, find the file named `debug.log`. This is the file we are looking for. You can open this with any text editor and share the file with others.
