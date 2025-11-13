---
title: Stonewall x2
date: 2025-11-13 12:30:00 +0000
categories: [Guide, Privacy]
tags: [bitcoin,wallets,privacy,samourai,ashigaru]     # TAG names should always be lowercase
description: Understand and use Stonewall x2 transactions in Ashigaru
# comments: false
#pin: true
authors: [vibrant, loicpandul]
image: /assets/img/ashigaru-mobile-guide/header.png
---

> Make every spend a coinjoin.

## What is a Stonewall x2 transaction?

Stonewall x2 is a specific form of Bitcoin transaction that increases user privacy by collaborating with a third party who is not involved in the actual payment. This method simulates a mini‑coinjoin between two participants while making a payment to a third party. Stonewall x2 transactions are available in the [Ashigaru Mobile app](https://byakugan.io/posts/ashigaru-mobile-guide), a fork of Samourai Wallet (the team that originally created this transaction type).

Mechanics:
- You use one of your own UTXOs to fund the payment.
- A collaborator contributes one of their own UTXOs.
- The transaction has four outputs:
  - Two equal‑amount outputs: one goes to the payment recipient; the other returns to the collaborator.
  - A third output returns the rest of the collaborator’s input (so their net is neutral, minus mining fees).
  - A fourth output is your change.

Roles in Stonewall x2:
- The sender (payer), who makes the actual payment
- The collaborator, who contributes bitcoin to improve the transaction’s anonymity set and receives all their funds back (net‑neutral, minus mining fees)
- The recipient, who can ignore the transaction’s structure and simply receives a payment

Example: Alice buys a loaf for 4,000 sats and wants privacy. She collaborates with her friend Bob.

![image](/assets/img/stonewallx2/01.webp)

Analyzing the transaction:
- The baker receives 4,000 sats
- Alice inputs 10,000 sats and receives 6,000 sats as change (net −4,000 sats)
- Bob inputs 15,000 sats and receives two outputs: 4,000 sats and 11,000 sats (net 0)

> For clarity, mining fees are omitted in the example. In practice, transaction fees are split equally between the sender and the collaborator.
{: .prompt-info }

## What’s the difference between Stonewall and Stonewall x2?

A Stonewall x2 works exactly like a Stonewall in pattern, except that x2 is collaborative while Stonewall is not. Stonewall x2 involves a third party (outside the payment) who contributes bitcoin to boost privacy. In a regular Stonewall, the “collaborator’s role” is played by the sender alone.

Continuing the bakery example: if Alice didn’t find a collaborator like Bob, she could do a Stonewall by herself — both inputs would be hers, and she would receive three change outputs.

![image](/assets/img/stonewallx2/02.webp)

From the outside, the pattern would look the same:

![image](/assets/img/stonewallx2/05.webp)

Practical logic for Ashigaru spending tools:
- If the merchant does not support Payjoin/Stowaway, you can perform a collaborative spend (Stonewall x2) with an external participant.
- If you don’t find a collaborator, you can do a solo Stonewall that mimics the Stonewall x2 pattern.

> Both transactions share the same structure on‑chain. The difference (collaborative vs non‑collaborative) is invisible to observers, which increases doubt — and privacy.
{: .prompt-tip }

## Why use a Stonewall x2 transaction?

Stonewall x2 adds significant entropy to the transaction and confuses chain‑analysis heuristics. From the outside, it can be misinterpreted as a small two‑party coinjoin. In reality, it’s a payment with a collaborator who remains net‑neutral.

Returning to Alice, Bob, and the Baker, the blockchain view might look like:

![image](/assets/img/stonewallx2/03.webp)

A typical heuristic‑driven conclusion could be:

“**Alice and Bob did a small coinjoin, one input each, two outputs each.**”

![image](/assets/img/stonewallx2/04.webp)

But the actual situation is different: one output is the baker’s payment, Alice has one change output, and Bob has two outputs returning his contribution.

![image](/assets/img/stonewallx2/01.webp)

Even if the observer identifies the Stonewall x2 pattern, they cannot know:
- Which of the two equal‑amount outputs is the payment
- Whether Alice or Bob made the payment
- Whether the two inputs came from two different people or one person combining UTXOs

Because Stonewall (solo) and Stonewall x2 (collaborative) share the same pattern, distinguishing them without extra context is impossible, adding more doubt to the spend.

![image](/assets/img/stonewallx2/05.webp)

> The ambiguity about who paid and who contributed which inputs/outputs is the privacy benefit.
{: .prompt-info }

## How to establish a connection between PayNyms

Like other collaborative Ashigaru transactions (Cahoots), Stonewall x2 requires exchanging partially signed transactions (PSBTs) between the sender and the collaborator. This can be done:
- Manually, when you’re physically together (QR code exchange)
- Automatically, via the Soroban communication protocol

For Soroban (online) collaboration, you must establish a PayNym connection first: your PayNym must “follow” your collaborator’s PayNym, and vice versa. See the beginning of this tutorial:

> Soroban uses BIP47 channels between PayNyms to exchange PSBTs securely. Verify you’re following each other before initiating.
{: .prompt-tip }

## How to do a Stonewall x2 in Ashigaru

Tap your PayNym image (top‑left), then open the “Collaborate” menu. Your collaborator must do the same — unless you’re exchanging QR codes in person.

![Image](/assets/img/stonewallx2/06.webp)

Choose:
- “Initiate” if you are the payer
- “Participate” if you are the collaborator (not the payer or recipient)

![Image](/assets/img/stonewallx2/07.webp)

If you’re the collaborator (remote via Soroban):
- Tap “Participate”
- Choose the account to use
- Tap “LISTEN FOR CAHOOTS REQUESTS” and wait for the payer’s request

![Image](/assets/img/stonewallx2/08.webp)

For in‑person collaboration:
- On your wallet home, tap the QR icon at the top
- Scan the QR provided by the payer who initiated the transaction

![Image](/assets/img/stonewallx2/09.webp)

If you’re the payer (initiating):
- Go to “Collaborate” and select “Initiate”
- Choose the transaction type: “STONEWALL X2”

![Image](/assets/img/stonewallx2/10.webp)
![Image](/assets/img/stonewallx2/11.webp)

Choose collaboration mode:
- “Online” (Cahoots via Soroban)
- “In Person / Manual” (QR exchange)

![Image](/assets/img/stonewallx2/12.webp)

### Online Cahoots (Soroban)

Select your collaborator from the PayNyms you follow.

![Image](/assets/img/stonewallx2/13.webp)

Tap “Set up transaction,” then choose the account to spend from.

![Image](/assets/img/stonewallx2/14.webp)

On the next page, enter:
- The real recipient’s address
- The amount to send
- The fee rate

Tap “Review transaction setup.”

![Image](/assets/img/stonewallx2/15.webp)

Review carefully. Ensure your collaborator is “listening for Cahoots requests,” then tap the green “BEGIN TRANSACTION” button to initiate the Soroban PSBT exchange.

![Image](/assets/img/stonewallx2/16.webp)

Wait until both participants finish signing. Then broadcast to the Bitcoin network.

![Image](/assets/img/stonewallx2/17.webp)

> Keep the app open and online during the Soroban flow; interruptions will pause the PSBT exchange.
{: .prompt-info }

### In‑person exchange (QR)

Select the transaction type “STONEWALL X2,” then choose “In Person / Manual.”

![Image](/assets/img/stonewallx2/18.webp)

Tap “Set up transaction,” then choose the account to spend from.

![Image](/assets/img/stonewallx2/19.webp)

Enter:
- The real recipient’s address
- The amount
- The fee rate

Tap “Review transaction setup.”

![Image](/assets/img/stonewallx2/20.webp)

Review, then tap the green “BEGIN TRANSACTION” to start the PSBT exchange via QR scanning.

![Image](/assets/img/stonewallx2/21.webp)

Alternate scanning with the collaborator:
- Tap “SHOW QR CODE” so your collaborator can scan it
- Then the collaborator taps “SHOW QR CODE” for you to scan
- You tap “LAUNCH QR Scanner” to scan theirs
- Repeat until all five exchange steps are complete

![Image](/assets/img/stonewallx2/22.webp)

After all exchanges, verify the transaction details, then slide the green arrow at the bottom to broadcast.

![Image](/assets/img/stonewallx2/23.webp)

[The transaction has been broadcast](https://mempool.space/testnet4/tx/9082f3d989728aacd290535a1ac374ab8c04a241a1d798b378db626dabea7a24). Its structure looks like this:

![Image](/assets/img/stonewallx2/e.png)

Credit: [mempool.space](https://mempool.space/)

Observations:
- Two inputs are mine: 91,869 sats and 64,823 sats
- Two inputs are from the collaborator’s wallet
- Outputs:
  - 100,000 sats goes to the real recipient
  - 100,000 sats and 159,578 sats return to the collaborator (net‑neutral, minus mining fees)
  - 56,270 sats is my change, the difference between my total inputs and the 100,000 sats payment

I can describe this structure since I built the transaction; but for an external observer, it’s generally impossible to determine which UTXOs belong to which participant — either on inputs or outputs.

> Stonewall x2 blurs input ownership and destination heuristics. Observers cannot reliably ascribe roles, adding doubt and protecting privacy.
{: .prompt-tip }