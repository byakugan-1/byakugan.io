---
title: Ashigaru Stonewall
date: 2025-11-13 12:30:00 +0000
categories: [Guide, Privacy]
tags: [bitcoin,wallets,privacy,samourai,dojo,ashigaru,terminal]     # TAG names should always be lowercase
description: Understand and use Stonewall transactions in Ashigaru
# comments: false
#pin: true
authors: [vibrant, loicpandul]
image: /assets/img/ashigaru-mobile-guide/header.png
---

> Break the assumptions of blockchain analysis with mathematically provable doubt between the sender and the recipient of your transactions.

## What is a Stonewall transaction?

Stonewall is a specific form of Bitcoin transaction that boosts user privacy by imitating a two‑party coinjoin — without actually being one. The transaction is not collaborative. A single user can construct it alone, using only their own UTXOs as inputs. You can create a Stonewall transaction anytime, without needing to coordinate with another user.

Mechanics:
- Inputs: the sender uses 2 of their own UTXOs
- Outputs: the transaction produces 4 outputs, including 2 outputs that are exactly the same amount; the other 2 are change
- Among the 2 equal‑amount outputs, only one goes to the actual payment recipient

There are only two roles in a Stonewall transaction:
- The sender, who makes the actual payment
- The recipient, who can ignore the specific nature of the transaction and simply expects a payment

Let’s use a simple example. Alice buys a loaf of bread for 4,000 sats and wants to pay in bitcoin while keeping her payment private. She decides to build a Stonewall transaction.

![image](/assets/img/ashigaru-stonewall/01.webp)

Analyzing this transaction, the baker receives 4,000 sats for the bread. Alice used two inputs: 10,000 sats and 15,000 sats. The outputs on Alice’s side are 4,000 sats, 6,000 sats, and 11,000 sats. Alice’s net balance change is −4,000 sats, which matches the price.

> For clarity, the example omits mining fees. In reality, the sender pays all transaction fees.
{: .prompt-info }

## What’s the difference between Stonewall and Stonewall x2?

Stonewall and Stonewall x2 share the same external pattern. The difference is collaboration:
- Stonewall is non‑collaborative — the sender creates it alone
- Stonewall x2 is collaborative — a second participant (“the collaborator”) contributes an input and receives their entire contribution back in the outputs (minus fees)

Continuing the bakery example: if Alice wanted Stonewall x2, she would collaborate with Bob. Each would contribute one input. Bob would receive back all his input on the outputs. The baker would receive the 4,000 sats payment as before, and Alice would end up with her initial balance minus the bread cost.

![image](/assets/img/ashigaru-stonewall/02.webp)

From the outside, the transaction pattern appears identical.

![image](/assets/img/ashigaru-stonewall/03.webp)

In short: Stonewall and Stonewall x2 share the same structure. Stonewall is built alone; Stonewall x2 involves cooperation between two users.

[Learn more about Stonewall x2](https://byakugan.io/posts/stonewallx2)

> Observers cannot distinguish Stonewall from Stonewall x2 with on‑chain data alone. This ambiguity is the point — more doubt, more privacy.
{: .prompt-info }

## Why use a Stonewall transaction?

The Stonewall structure adds meaningful entropy and confuses common chain‑analysis heuristics. From the outside, it can look like a small two‑party coinjoin. But in reality (like Stonewall x2), it’s just a payment. This duality introduces uncertainty and can lead watchers down false trails.

Returning to Alice’s bakery purchase, the blockchain view might look like this:

![image](/assets/img/ashigaru-stonewall/04.webp)

A typical heuristic‑driven conclusion could be: “Two people performed a small coinjoin, each with one input and two outputs.”

![image](/assets/img/ashigaru-stonewall/05.webp)

But you know the truth: one output went to the baker, both inputs belong to Alice, and she received three outputs of change.

![image](/assets/img/ashigaru-stonewall/01.webp)

Even if an observer recognizes the Stonewall pattern, they still cannot know:
- Which of the two equal‑amount outputs is the payment
- Whether the two inputs belong to two different people or to one person combining them

The second point exists because Stonewall x2 (collaborative) and Stonewall (non‑collaborative) share the same pattern. Without additional context, differentiating them is impossible, which adds yet more doubt to the spend.

![image](/assets/img/ashigaru-stonewall/03.webp)

> Stonewall introduces uncertainty about input ownership and payment destination. This uncertainty is privacy.
{: .prompt-tip }

## How to do a Stonewall transaction in Ashigaru

Stonewall was originally developed by the Samourai Wallet team and is now implemented in Ashigaru, the fork created after the Samourai developers’ arrest. Install Ashigaru and create a wallet:

[Ashigaru Mobile guide](https://byakugan.io/posts/ashigaru-mobile-guide)

Unlike Stowaway or Stonewall x2 (“cahoots”), Stonewall does not require PayNyms. It can be executed directly, without preparation or collaboration.

In practice, you don’t need a guide just to make Stonewall — Ashigaru generates Stonewall automatically for each spend when your wallet has sufficient UTXOs.

Tap the “+” in the bottom‑right, then select “Send.”

![Image](/assets/img/ashigaru-stonewall/06.webp)

Choose the account to spend from.

![Image](/assets/img/ashigaru-stonewall/07.webp)

Enter the transaction details: recipient address and amount to send, then press the arrow to confirm.

![Image](/assets/img/ashigaru-stonewall/08.webp)

You can adjust default fees for current network conditions. The most interesting element here is the transaction type: Ashigaru automatically selected “STONEWALL.” Tap “PREVIEW” for more details.

![Image](/assets/img/ashigaru-stonewall/09.webp)

You’ll see the Stonewall pattern: 2 inputs of equal amount, 2 outputs of equal amount, plus change outputs, and (in this example) an additional input to reach the payment sum.

![Image](/assets/img/ashigaru-stonewall/10.webp)

If you prefer a simple payment, tap the pencil icon (top‑right), then switch “STONEWALL” to “Simple.”

![Image](/assets/img/ashigaru-stonewall/11.webp)

After checking all details, slide the green arrow at the bottom to sign and broadcast.

![Image](/assets/img/ashigaru-stonewall/12.webp)

You now know how to make a Stonewall transaction and, more importantly, how it works. To further your privacy, read the [Ashigaru Terminal guide](https://byakugan.io/posts/ashigaru-whirlpool-guide) explaining coinjoins via Whirlpool: