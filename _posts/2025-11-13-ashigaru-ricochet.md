---
title: Ashigaru Ricochet
date: 2025-11-13 12:30:00 +0000
categories: [Guide, Privacy]
tags: [bitcoin,wallets,privacy,samourai,ashigaru]     # TAG names should always be lowercase
description: Understand and use Ricochet Within Ashigaru
# comments: false
#pin: true
authors: [vibrant, loicpandul]
image: /assets/img/ashigaru-mobile-guide/header.png
---

> A premium tool that adds extra hops of history to your transaction. Stump blacklists and help guard against unjust third‑party account closures.

## What is Ricochet?

Ricochet is a technique where you create several self‑payments (to your own fresh addresses) to simulate a change of ownership of your bitcoin before the final spend. Unlike Ashigaru’s other spending tools (inherited from Samourai Wallet), Ricochet does not aim for prospective anonymity; instead, it provides a form of retrospective anonymity. In practice, Ricochet blurs properties that could compromise the fungibility of a Bitcoin UTXO.

For example, if you perform a coinjoin, your postmix coin will be identifiable as having passed through a coinjoin. Chain‑analysis tools can detect coinjoin patterns and tag coins that exit them. Coinjoins break historical links, but their presence is still detectable — like encrypted text: you can’t read it, but it’s easy to see that encryption was applied.

That “coinjoin‑tagged coin” label can affect fungibility. Regulated entities (e.g., exchanges) may refuse coinjoin‑sourced UTXOs, ask for explanations, or even freeze accounts/funds. In some cases, an exchange may report activity to state authorities.

Ricochet addresses this by inserting four successive self‑transactions, each to a new address you control, then sending to the final destination (e.g., an exchange). The goal is to create distance between the original coinjoin and the final spend. This makes chain‑analysis tools more likely to consider a change of ownership has occurred post‑coinjoin, discouraging them from taking action against the sender.

![image](/assets/img/ashigaru-ricochet/01.webp)

You might ask why chain‑analysis tools don’t simply look beyond four hops. In practice, these companies face an optimization dilemma: they must choose a threshold for “number of hops” after which they assume a change of ownership likely occurred and ignore older links. Raising that threshold increases false positives exponentially — wrongly flagging people as coinjoin participants when someone else did the coinjoin earlier in the chain. Too many false positives push users to competitors and threaten long‑term viability. As a result, raising the threshold is challenging; four hops is often enough to defeat their heuristics in many cases.

> Ideally, do not send coinjoin‑sourced coins to regulated entities. If you must (e.g., urgent fiat liquidation), Ricochet can help reduce misclassification risks.
{: .prompt-warning }

> Ricochet is a pragmatic, retrospective privacy tool. It does not guarantee acceptance by any third party, but it commonly reduces friction with blacklist heuristics.
{: .prompt-info }

## How Ricochet works in Ashigaru

Ricochet is simply sending bitcoin to yourself; you can simulate it manually without any specialized tool. Ashigaru (a fork of Samourai Wallet) offers a streamlined, automated Ricochet that produces clean results.

- Service cost: Ricochet on Ashigaru charges 100,000 sats for service fees, plus mining fees.
- Practical use: Best suited for larger transfers where the fee overhead is proportionally reasonable.

> Because Ricochet costs 100,000 sats plus mining fees, it’s recommended for significant amounts rather than small spends.
{: .prompt-warning }

Ashigaru offers two Ricochet variants:

- Staggered delivery (reinforced Ricochet)
  - Distributes the 100,000‑sat service fee across the five successive transactions
  - Ensures each transaction is broadcast at a distinct time and confirms in a different block
  - Maximizes the appearance of ownership change for better resistance to chain analysis
  - Slower, but preferred when you’re not in a hurry

- Classic Ricochet
  - Executes quickly, broadcasting transactions within a short interval
  - Offers less privacy and resistance to analysis than staggered delivery
  - Use for urgent sends only

> Choose “staggered delivery” for best privacy; choose “Classic” only if you need speed.
{: .prompt-tip }

## How to do a Ricochet in Ashigaru

1) Start a send

- Tap “+” → “Send”
- Select the account to spend from

![Image](/assets/img/ashigaru-ricochet/02.webp)

2) Fill transaction details

- Enter the amount to send
- Enter the final destination address (the address after the hops)
- Check the “Ricochet” option

![Image](/assets/img/ashigaru-ricochet/03.webp)

3) Choose Ricochet mode

- Select “Classic” (faster, lower privacy) or “staggered delivery” (slower, higher privacy)
- Tap the arrow at the bottom to confirm

![Image](/assets/img/ashigaru-ricochet/04.webp)

4) Review and fee management

- On the summary screen, review all details
- Adjust miner fees according to current market conditions
- Slide the green arrow to sign and broadcast the Ricochet sequence

![Image](/assets/img/ashigaru-ricochet/05.webp)

5) Wait for the automatic hops to complete

![Image](/assets/img/ashigaru-ricochet/06.webp)

6) Confirm success and final delivery

![Image](/assets/img/ashigaru-ricochet/07.webp)

> Ricochet will automatically manage the sequence of hops. If you chose staggered delivery, allow time for each hop to confirm in a separate block.
{: .prompt-info }