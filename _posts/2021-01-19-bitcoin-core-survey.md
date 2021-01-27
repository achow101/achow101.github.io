---
layout: post
title: "Announcing the Bitcoin Core Usage Survey"
date: 2021-01-19 15:30:00 -0400
---
# Announcing the Bitcoin Core Usage Survey

Today I am launching a survey aimed at gathering information about how people use Bitcoin Core.

[Click here to take the survey](https://survey.alchemer.com/s3/6081474/8acd79087feb)

This survey is generously made possible by a grant from MIT's Digital Currency Initiative and is being run with the help and collaboration of the researchers at [Maiden Laps](https://maiden.global/).
A special thanks to Square Crypto [grant recipient](https://twitter.com/sqcrypto/status/1338665428550365184) Jamaal Montasser for his input throughout the design as well.

The survey will remain up for 6 weeks, until March 2nd, 2021.

## Goals

The goal of this survey is to learn why people use Bitcoin Core, how they use it, and what features are used.
The results of the survey will be used to inform what features should be focused on and prioritized.
By understanding how our users behave and use Bitcoin Core, we will be better able to write software that suits their needs.

Additionally the survey will allow users to provide feedback, such as feature requests, so that we can include things that people want.

## Results

The plan is to share the results of this survey publicly.
At the very least, the results will be shared with other Bitcoin Core developers so that many of the people involved will have data that can be used.

## Privacy

As many people involved in Bitcoin are very privacy conscious, this survey was designed in order to preserve survey taker's privacy as much as possible.
The survey uses [Alchemer](https://www.alchemer.com/) and is configured so that no information about the survey taker is stored.
While the survey does ask for location, survey source, and email, these are all entirely optional and can be left blank if so desired.
Additionally IP information is not collected and the survey does not make use of any kind of tracking cookies.
Even so, if you want to preserve your privacy as much as possible, I recommend using the [Tor browser](https://www.torproject.org/) to take it.
I have tested it with Tor and it does not appear to have any issues with the website; no annoying CAPTCHAs to fill out and the web page works as expected.

## Survey Breakdown

In this section, I will go through the various questions being asked and what the purpose of asking them is.
There are some questions which change which questions are asked.
These are intended to accommodate the wide variety of participants that we expect so that only relevant questions will be answered.

### Meta and Screener Questions

The questions in this section just provide some additional context about our survey participants as well as determining which questions are relevant to the participant.

* Which country are you based in?
* Where did you hear about this study?

These two questions allow us to control for potential bias due to the source of the participants (e.g. users that come from Reddit may be different from those who come from Twitter) as well as gain some insight into how users are distributed around the world.

* Do you run a node with Bitcoin Core?
* Do you use the Bitcoin Core Wallet?

These two questions direct the participant to different questions.
Depending on the answer, different questions will be asked as some are not relevant to those who, for example, do not use the Bitcoin Core wallet.

### Currently Node Operator Questions

* Why do you own and/or use Bitcoin?
* Why do you run a Bitcoin Core node?

These questions aim to provide us with the motivations for users to run a node.
This will help us change how the node behaves so that it can better fit with why people are using Bitcoin Core as a node.

* How long have you run a Bitcoin Core node?
* How often do you update your Bitcoin Core node?
* What version are you running?

These questions give us information about how frequently people actually update their node software as well as telling us what versions are actually in use.
This will help us with deciding when and whether a new bug fix release should be made.

* Select core features you've used on Bitcoin Core's node over the last six months
* If applicable, select all of the settings for which you have changed the default value

These two questions inform us as to what features people are using.
This will help us know where to prioritize ongoing development.
For some features, this could be informative as to whether a feature should be removed or changed.

* What other Bitcoin software or hardware projects do you often use, if any?

Knowing what other software is being used can help us learn where there are deficiencies in Bitcoin Core that should be remedied.
This would let us know what features we may want to implement as people are using other software for those features.

* When you run into problems with your Core node, how do you usually troubleshoot those problems?
* What's the primary way you stay informed of Bitcoin Core development?

These questions allow us to learn where users are getting their information.
This will help us with knowing where to go to distribute information in the event that something needs to be widely known.
Additionally this lets us know where developers may need to increase their presence in order to better aid users.

### Previously Operated Node Questions

Many of the questions for this section are the same as for the Current Node Operator questions.

* When did you stop running a Bitcoin Core node?
* Why did you run a Bitcoin Core node?
* What's the reason why you stopped running a Bitcoin Core node?

These questions will inform us what stops people from running a Bitcoin Core node so that we can improve.

### Currently Use the Wallet Questions

* What is most important to you when looking for a new wallet?

This question allows us to know what users want so that we can focus on desired features.

* How did you find yourself primarily using the Bitcoin Core wallet?
* How do you primarily use the Bitcoin Core wallet?
* Is Bitcoin Core the only wallet you use?
* What other wallets do you use and why do you use them instead of the Core wallet?

These two questions tell us how users use the wallet.
With this information, we will be able to decide which set of features should be prioritized or more actively worked on.
Knowing what other software is being used also lets us know what is missing in Bitcoin Core that we may want to emulate.

* What does the Bitcoin Core wallet do better than other bitcoin wallets you have tried, if any?
* Select features you've used on Bitcoin Core's wallet over the last six months?
* What feature would you be most disappointed to see removed from the Bitcoin Core wallet?
* What features or actions did you wish the Bitcoin Core wallet supported better?

This questions lets us know what features we should prioritize, keep, and improve.

* How many UTXOs are in your Bitcoin Core wallet?

This question is specifically to aid with the design of our coin selection algorithm.
The coin selection algorithm is what decides what inputs to use in a transaction and is an integral part of the wallet.
It can be optimized to different kinds of wallets.
This question allows us to know what kind of wallets the coin selection algorithm should be targeting to better serve the majority of our users.

### Previously Used the Wallet Questions

Many of the questions for this section are the same as for the Currently Use the Wallet questions.

* How long were you using the Bitcoin Core wallet for?
* When did you stop using the Bitcoin Core wallet?

These questions are to learn how long people tend to use the wallet for and whether stopping the use of the wallet is related to the node.

* What's the reason you no longer use the Bitcoin Core wallet?
* What wallet did you switch to?

We want to know what turned users off to another wallet so that we can make the wallet better to meet the needs that others were fulfilling.

### Never Used the Wallet Questions

* What wallet do you use?
* How did you decide which wallet to use?
* Did you consider using the Bitcoin Core wallet?
* Do you remember why you decided to not use the Bitcoin Core wallet?
* What is most important to you when looking for a new wallet?
* What feature would you be most disappointed to see removed from your current wallet?

Although users who see these questions do not use Bitcoin Core for their wallet, we would like to know why and whether there are things from the wallet that they use that we should implement in Bitcoin Core.

### Never Used Bitcoin Core Questions

* Do you own Bitcoin?
* What wallet do you use to store Bitcoin?
* Why do you own and/or use Bitcoin?
* Have you considered running a Bitcoin Node?
* What's stopping you from running a node?

Although users who see these questions do not use Bitcoin Core, it is still useful for us to know why they do not so that we can hopefully address those concerns and onboard new users.

## Acknowledgements

Special thanks to MIT's DCI for generously sponsoring this survey, and for the researchers at [Maiden Labs](https://maiden.global/), particularly Zach Herring and Shira Frank, who helped me put this survey together.
Additional thanks to Jamaal Montasser, Samuel Dobson, Sjors Provoost, and Jeremy Rubin who all provided additional insight into the questions and goals of this survey.
