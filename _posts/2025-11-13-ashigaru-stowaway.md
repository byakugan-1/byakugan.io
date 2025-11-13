---
title: Ashigaru Stowaway (PayJoin)
date: 2025-11-13 12:30:00 +0000
categories: [Guide, Privacy]
tags: [bitcoin,wallets,privacy,samourai,ashigaru]     # TAG names should always be lowercase
description: What Payjoin is, and how to do it with Ashigaru Stowaway
# comments: false
#pin: true
authors: [vibrant, loicpandul]
image: /assets/img/ashigaru-mobile-guide/header.png
---

> Force blockchain spies to rethink everything they think they know.

## Understanding Payjoin transactions on Bitcoin

Payjoin is a specific structure of Bitcoin transaction that enhances user privacy during a payment by collaborating with the payment recipient.

In 2015, [LaurentMT](https://twitter.com/LaurentMT) first described this method as “steganographic transactions” in a document available [here](https://gist.githubusercontent.com/LaurentMT/e758767ca4038ac40aaf/raw/c8125f6a3c3d0e90246dc96d3b603690ab6f1dcc/gistfile1.txt). Samourai Wallet adopted and implemented it as “Stowaway” in 2018. Payjoin concepts are also discussed in [BIP79](https://github.com/bitcoin/bips/blob/master/bip-0079.mediawiki) and [BIP78](https://github.com/bitcoin/bips/blob/master/bip-0078.mediawiki). Common terms you may see:

- Payjoin
- Stowaway
- P2EP (Pay‑to‑End‑Point)
- Steganographic transaction

What makes Payjoin unique is that it produces a transaction that looks ordinary at first glance but is actually a mini coinjoin between two parties. To achieve this, the recipient participates in the inputs alongside the sender. The recipient also includes a self‑payment in the transaction, allowing them to be paid.

Example: if you buy a baguette for 4,000 sats using a 10,000‑sat UTXO and opt for a Payjoin, the baker adds a 15,000‑sat UTXO that belongs to them as an input. They receive that in full as an output, plus your 4,000‑sat payment:

![Payjoin transaction diagram](/assets/img/payjoin/1.webp)

In this example, the baker introduces 15,000 sats as input and receives 19,000 sats as output — a difference of exactly 4,000 sats, the price of the baguette. You input 10,000 sats and end with 6,000 sats in change — a −4,000‑sat balance, i.e., the payment amount.

> For clarity, mining fees are omitted here. In practice, they are included and paid by the parties.
{: .prompt-info }

## What is the purpose of a Payjoin transaction?

Payjoin achieves two privacy goals:

A Payjoin transaction serves two objectives that allow users to enhance the privacy of their payment. First of all, Payjoin aims to mislead an external observer by creating a decoy in chain analysis. This is made possible through the Common Input Ownership Heuristic (CIOH). Usually, when a transaction on the blockchain has multiple inputs, it is assumed that all these inputs likely belong to the same entity or user. Thus, when an analyst examines a Payjoin transaction, they are led to believe that all the inputs come from the same person. However, this perception is incorrect because the payment recipient also contributes inputs alongside the actual payer. Therefore, chain analysis is diverted towards an interpretation that turns out to be false.

Furthermore, Payjoin also allows for deceiving an external observer about the actual amount of the payment that has been made. By examining the transaction structure, the analyst might believe that the payment is equivalent to the amount of one of the outputs. However, in reality, the payment amount does not correspond to any of the outputs. It is actually the difference between the recipient's output UTXO and the recipient's input UTXO. In this sense, the Payjoin transaction falls into the domain of steganography. It allows for hiding the actual amount of a transaction within a fake transaction that acts as a decoy.

Definition — Steganography:
> Steganography is a technique of concealing information within other data or objects in such a way that the presence of the hidden information is not perceptible. For example, a secret message can be hidden inside a dot in a text that has nothing to do with it, making it undetectable to the naked eye (this is the technique of micropoint). Unlike encryption, which makes information incomprehensible without the decryption key, steganography does not modify the information. It remains displayed in plain sight. Its objective is rather to hide the existence of the secret message, whereas encryption clearly reveals the presence of hidden information, although inaccessible without the key.
{: .prompt-tip }

Returning to our baguette example:

![Payjoin transaction schema from the outside](/assets/img/payjoin/2.webp)

A typical heuristic might conclude: “Alice merged 2 UTXOs to pay 19,000 sats to Bob.”

![Incorrect interpretation of Payjoin transaction](/assets/img/payjoin/3.webp)

That’s wrong. The two input UTXOs do not belong to one person, and the actual payment value is 4,000 sats — the difference between Bob’s output and Bob’s input. The observer’s analysis is led to an erroneous conclusion, protecting participants’ privacy.

![Payjoin transaction diagram](/assets/img/payjoin/1.webp)

If you want to analyze a real Payjoin, here’s one performed on testnet:
[8dba6657ab9bb44824b3317c8cc3f333c2f465d3668c678691a091cdd6e5984c](https://mempool.space/fr/testnet/tx/8dba6657ab9bb44824b3317c8cc3f333c2f465d3668c678691a091cdd6e5984c)

External resources:
- [LaurentMT steganography gist](https://gist.githubusercontent.com/LaurentMT/e758767ca4038ac40aaf/raw/c8125f6a3c3d0e90246dc96d3b603690ab6f1dcc/gistfile1.txt)
- [BIP78: Payjoin](https://github.com/bitcoin/bips/blob/master/bip-0078.mediawiki)
- [payjoin.org](https://payjoin.org/)
- BTC 204 — Privacy on Bitcoin: Part 5
  [https://planb.academy/courses/65c138b0-4161-4958-bbe3-c12916bc959c](https://planb.academy/courses/65c138b0-4161-4958-bbe3-c12916bc959c)

---

Payjoin is implemented in Ashigaru as “Stowaway.” Below is how Stowaway works and how to use it step by step.

## How does Stowaway work?

Ashigaru includes a Payjoin tool called Stowaway, available in the Ashigaru Android app. To complete a Payjoin, the recipient (who also acts as the collaborator) must use software compatible with Stowaway — currently, Ashigaru only.

Stowaway belongs to Samourai’s “Cahoots” category — collaborative transactions that exchange information off‑chain. Ashigaru currently offers two Cahoots tools: Stowaway (Payjoins) and [Stonewall X2](https://byakugan.io/posts/stonewallx2).

Cahoots require exchanging PSBTs (partially signed transactions) between users. Manually, this involves five successive QR scans between participants (suitable when you’re together in person). At a distance, manual exchange is cumbersome; Soroban (an encrypted Tor‑based protocol) automates the PSBT exchange in the background.

Soroban requires an authenticated channel between participants. It uses users’ PayNyms for identification and encrypted communications.

A PayNym is a unique wallet identifier that activates features like encrypted exchanges. It appears as an ID with an illustration (example on testnet):

![Example PayNym](/assets/img/ashigaru-stowaway/01.webp)

Summary:
- Payjoin = specific collaborative transaction structure
- Stowaway = Ashigaru’s Payjoin implementation
- Cahoots = Samourai’s name for collaborative transaction types (Stowaway, Stonewall X2), now in Ashigaru
- Soroban = Tor‑based encrypted communications for Cahoots
- PayNym = unique wallet identifier used to establish Soroban communications for Cahoots

> Soroban automates PSBT exchange over Tor; PayNyms provide the authenticated channel used by Soroban.
{: .prompt-info }

## How to establish a connection between PayNyms

First, [install Ashigaru](https://byakugan.io/posts/ashigaru-mobile-guide) and create a wallet:

To do a remote Cahoots (Payjoin/Stowaway), you must “follow” the user you’ll collaborate with, using their PayNym — in Stowaway, this means following the person you intend to pay.

> Before initiating Stowaway, ensure both PayNyms follow each other — it’s required to establish the encrypted Soroban channel.
{: .prompt-tip }

## How to do a Payjoin in Ashigaru

Tap your PayNym image (top‑left), then open “Collaborate.” The person participating must do the same — unless you’re exchanging QR codes in person.

![Collaborate menu](/assets/img/ashigaru-stowaway/02.webp)

Choose:
- “Initiate” if you are the payer (sender)
- “Participate” if you are the recipient (collaborator)

![Initiate or Participate](/assets/img/ashigaru-stowaway/03.webp)

If you’re the recipient/collaborator (remote via Soroban):
- Tap “Participate”
- Choose the account you’ll use
- Tap “LISTEN FOR CAHOOTS REQUESTS” and wait for the payer’s request

![Listen for requests](/assets/img/ashigaru-stowaway/04.webp)

For in‑person collaboration:
- Go to wallet home
- Tap the QR icon (top)
- Scan the payer’s QR code

![Scan QR](/assets/img/ashigaru-stowaway/05.webp)

If you’re the payer (initiator):
- Go to “Collaborate” → “Initiate”

![Initiate](/assets/img/ashigaru-stowaway/06.webp)

- Choose the transaction type “Stowaway” (Payjoin)

![Choose Stowaway](/assets/img/ashigaru-stowaway/07.webp)

- Choose collaboration mode:
  - “Online” (Cahoots via Soroban)
  - “In Person / Manual” (QR exchanges)

![Collaboration mode](/assets/img/ashigaru-stowaway/08.webp)

### Online Cahoots (Soroban)

Select the recipient from the PayNyms you follow.

![Select PayNym](/assets/img/ashigaru-stowaway/09.webp)

Tap “Set up transaction,” then choose the spending account.

![Choose account](/assets/img/ashigaru-stowaway/10.webp)

Enter:
- Amount to send
- Fee rate

No receiving address is needed — the recipient supplies it during the PSBT exchange. Tap “Review transaction setup.”

![Review setup](/assets/img/ashigaru-stowaway/11.webp)

Check details carefully. Ensure your collaborator is listening for Cahoots requests, then tap the green “BEGIN TRANSACTION” to start the Soroban PSBT exchange.

![Begin transaction](/assets/img/ashigaru-stowaway/12.webp)

Wait until both participants finish signing, then broadcast to the Bitcoin network.

![Broadcast](/assets/img/ashigaru-stowaway/13.webp)

> Keep both devices online and inside the app during Soroban — interruptions pause the PSBT exchange.
{: .prompt-info }

### In‑person exchanges (QR)

If you prefer a face‑to‑face exchange, select the transaction type “Stowaway,” then choose “In Person / Manual.”

![In person mode](/assets/img/ashigaru-stowaway/14.webp)

Tap “Set up transaction,” then choose the spending account.

![Choose account](/assets/img/ashigaru-stowaway/15.webp)

Enter:
- Amount to send
- Fee rate

No receiving address is needed — the recipient provides it during the PSBT exchange. Tap “Review transaction setup.”

![Review setup](/assets/img/ashigaru-stowaway/16.webp)

Tap the green “BEGIN TRANSACTION” to start the QR‑based PSBT exchange.

![Begin QR exchange](/assets/img/ashigaru-stowaway/17.webp)

Alternate scanning:
- Tap “SHOW QR CODE” so your collaborator can scan it
- They tap “SHOW QR CODE” for you to scan
- You tap “LAUNCH QR Scanner” to scan theirs
- Repeat until all five steps complete

![Alternate scans](/assets/img/ashigaru-stowaway/18.webp)

After all exchanges, verify the transaction details, then slide the green arrow to broadcast.

![Broadcast arrow](/assets/img/ashigaru-stowaway/19.webp)

[Broadcasted transaction (testnet)](https://mempool.space/testnet4/tx/82efd3700bba87b0f172e9cc045e441b38622c95a60df9f39a21f63eb4590a96)

Its structure looks like this:

![Structure diagram](/assets/img/ashigaru-stowaway/d.png)

Credit: [mempool.space](https://mempool.space)

Analysis:
- Inputs: my UTXO (164,211 sats) + recipient’s UTXO (190,002 sats)
- Outputs: my change (63,995 sats) + recipient’s output (290,002 sats)

Comparing inputs and outputs:
- Recipient gains 100,000 sats (payment amount)
- I lose 100,000 sats plus mining fees

I can describe this structure because I built the transaction; but for external observers, it’s generally impossible to determine which UTXOs belong to which participant — inputs or outputs.

> Stowaway blurs input ownership and destination; observers cannot reliably assign roles, which strengthens privacy.
{: .prompt-tip }