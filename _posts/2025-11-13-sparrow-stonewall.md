---
title: Sparrow Stonewall
date: 2025-11-13 12:30:00 +0000
categories: [Guide, Privacy]
tags: [bitcoin,wallets,privacy,samourai,ashigaru]     # TAG names should always be lowercase
description: Understand and use Stonewall transactions in Sparrow Wallet
# comments: false
#pin: true
authors: [vibrant, loicpandul]
image: /assets/img/sparrow-guide-images/header.png
---

> Break the assumptions of blockchain analysis with mathematically provable doubt between the sender and the recipient of your transactions.

## What is a Stonewall transaction?

Stonewall is a specific form of Bitcoin transaction designed to increase privacy by imitating a two‑party coinjoin — without actually being one. It is not collaborative. A single user can build it on their own, using only their UTXOs as inputs. You can create a Stonewall transaction for any payment, without coordinating with another person.

Mechanics:
- Inputs: the sender uses 2 of their own UTXOs
- Outputs: the transaction produces 4 outputs, including 2 outputs that are exactly the same amount; the other 2 are change
- Of the 2 equal‑amount outputs, only one is the actual payment to the recipient

Roles in a Stonewall:
- The sender, who makes the effective payment
- The recipient, who can ignore the specific structure and simply receives a payment

Example: Alice buys a loaf of bread for 4,000 sats and wants to keep her payment private. She decides to construct a Stonewall transaction.

![image](/assets/img/sparrow-stonewall/01.webp)

Analyzing this transaction:
- The baker receives 4,000 sats
- Alice’s inputs are 10,000 sats and 15,000 sats
- Alice’s outputs are 4,000 sats, 6,000 sats, and 11,000 sats
- Alice’s net change is −4,000 sats, matching the price of the bread

> For clarity, mining fees are omitted in the example. In practice, the sender pays all transaction fees.
{: .prompt-info }

## What’s the difference between Stonewall and Stonewall x2?

Stonewall and Stonewall x2 share the same external pattern. The difference is collaboration:

- Stonewall is non‑collaborative: the sender builds it alone
- Stonewall x2 is collaborative: a second participant (“the collaborator”) contributes an input alongside the sender’s input, then receives back the full contribution in the outputs (minus fees)

Continuing the bakery example: if Alice wanted to use Stonewall x2, she would collaborate with Bob. Each would add one input. Bob would receive back his entire input on the outputs. The baker would still receive 4,000 sats, and Alice would end up with her original balance minus the price of the bread.

![image](/assets/img/sparrow-stonewall/02.webp)

From the outside, the transaction pattern appears identical:

![image](/assets/img/sparrow-stonewall/03.webp)

In summary, Stonewall and Stonewall x2 share the same structure. The distinction is whether collaboration occurs. Stonewall is built individually; Stonewall x2 is set up by two participants.

[Learn more about Stonewall x2](https://byakugan.io/posts/stonewallx2)

> With on‑chain data alone, observers cannot distinguish Stonewall from Stonewall x2. This ambiguity adds doubt — and privacy.
{: .prompt-info }

## Why use a Stonewall transaction?

A Stonewall structure adds entropy and confuses chain‑analysis heuristics. From the outside, it can be interpreted as a small two‑party coinjoin. In reality (like Stonewall x2), it’s a payment. This creates uncertainty and can mislead heuristic‑based analysis.

Returning to Alice’s bakery purchase, the blockchain view might look like this:

![image](/assets/img/sparrow-stonewall/04.webp)

A typical heuristic‑driven conclusion could be:

“Two people did a small coinjoin — one input each, two outputs each.”

But the actual situation is different: one output is the baker’s payment; both inputs belong to Alice; and she receives three change outputs.

![image](/assets/img/sparrow-stonewall/01.webp)

Even if an observer recognizes the Stonewall pattern, they still cannot know:
- Which of the two equal‑amount outputs is the payment
- Whether the two inputs belong to two different people or to one person combining two UTXOs

Because Stonewall x2 (collaborative) and Stonewall (non‑collaborative) share the same pattern, and without contextual data, telling them apart is impossible — adding yet more doubt to the transaction.

![image](/assets/img/sparrow-stonewall/03.webp)

> Stonewall creates uncertainty about input ownership and payment destination — uncertainty that strengthens privacy.
{: .prompt-tip }

## How to make a Stonewall transaction in Sparrow

Stonewall was originally developed by the Samourai Wallet team and is implemented in Ashigaru (a fork created after the Samourai developers’ arrest) and in Sparrow Wallet.

[Install Sparrow and create a wallet](https://byakugan.io/posts/sparrow-guide)

Unlike Stowaway or Stonewall x2 (cahoots), Stonewall does not require PayNyms. It can be executed directly, without prior preparation or collaboration.

To create a Stonewall in Sparrow:
- Start a transaction as usual via the “Send” menu
- Or begin from the “UTXOs” menu if you want to use Coin Control

![Image](/assets/img/sparrow-stonewall/06.webp)

Enter the transaction details:
- Recipient address
- A label
- Amount to send
- Fee amount or fee rate based on current market conditions

![Image](/assets/img/sparrow-stonewall/07.webp)

Before confirming, select the Stonewall structure. At the bottom of the interface, switch from “Efficiency” to “Privacy.”

![Image](/assets/img/sparrow-stonewall/08.webp)

> If the “Privacy” option does not appear, your wallet likely does not have enough UTXOs to construct a Stonewall transaction.
{: .prompt-warning }

After choosing “Privacy,” you’ll see the structure changes: multiple UTXOs are consumed as inputs and the transaction produces two equal‑amount outputs — one is the actual payment (e.g., 100,000 sats) — plus change outputs.

![Image](/assets/img/sparrow-stonewall/09.webp)

If everything looks correct, click “Create Transaction.”

Then review all details again and click “Finalize Transaction for Signing.”

![Image](/assets/img/sparrow-stonewall/10.webp)

Sign the transaction using your wallet’s method, then click “Broadcast Transaction” to send it to the Bitcoin network for confirmation.

![Image](/assets/img/sparrow-stonewall/11.webp)

You now understand how a Stonewall transaction works in Sparrow Wallet and how to create one.