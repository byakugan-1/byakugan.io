---
title: Ashigaru - Whirlpool Coinjoin
date: 2025-11-04 12:30:00 +0000
categories: [Guide, Privacy]
tags: [bitcoin,wallets,privacy,samourai,dojo,ashigaru,terminal]     # TAG names should always be lowercase
description: How to Peform CoinJoins with Ashigaru Whirlpool
# comments: false
pin: true
authors: [vibrant, loicpandul]
image: /assets/img/ashigaru-whirlpool-guide/header.png
---

“a bitcoin wallet for the streets”

In this tutorial, you’ll learn what a coinjoin is in practice and how to perform one using the Ashigaru ecosystem and Whirlpool — a coinjoin protocol inherited from Samourai Wallet — via Ashigaru Terminal.

As always DO YOUR OWN RESEARCH!

## How Whirlpool coinjoins work

This guide does not explain coinjoin theory, why it’s useful, or the formal design of Whirlpool, as these topics will be covered in detail in a future post.

Here are a few quick points that will help in practice.

Wallets compatible with Whirlpool use 4 distinct accounts to support the coinjoin process:
- Deposit account, index`0'`
- Bad Bank (aka doxxic change), index`2 147 483 644'`
- Premix, index`2 147 483 645'`
- Postmix, index`2 147 483 646'`

As of November 2025 on Ashigaru, two pool denominations are available (this list will likely evolve in the coming months — check values at the time you read this):
-`0.25 BTC`, with an entry fee of`0.0125 BTC`
-`0.025 BTC`, with an entry fee of`0.00125 BTC`

Each mixing cycle usually includes 5 UTXOs as inputs and outputs.

![Image](/assets/img/ashigaru-whirlpool-guide/01.webp)

> Pool sizes and entry fees can change. Be sure to check current values before starting.
{: .prompt-info }

## Required software

To perform coinjoins with Whirlpool, you need three separate tools:

- [Ashigaru Terminal](https://byakugan.io/posts/ashigaru-terminal-setup-guide/) — manage your coinjoins directly from your computer

- [Ashigaru Mobile](https://byakugan.io/posts/ashigaru-mobile-guide/) — the mobile app you’ll use to spend your postmix bitcoin anywhere

- Dojo — a Bitcoin node implementation to ensure a sovereign connection without relying on third‑party servers, it can be run on:

    - Start9 by downloading dojo from the community marketplace (I would link to this but start9's marketplace website is poor to say the least)

    - Umbrel by installing the "[Samourai Server](https://apps.umbrel.com/app/samourai-server)" app

    - Vanilla dojo on Ubuntu or Debian by following [this guide](https://byakugan.io/posts/vanilla-dojo-guide/).

    - You may also use a public dojo run by a community member but know that this is not ideal for privacy and you should strive to run your own dojo. You may want to use a public dojo if you are just testing with testnet coins for example. Learn more here: [dojobay.pw](https://dojobay.pw/)

Install each tool following its dedicated tutorial. Then you can start doing your first coinjoins.

## Receive bitcoin

After creating your wallet, you start with a single account — index`0'` — called`Deposit`. Send bitcoin you intend to coinjoin to this Deposit account. You can receive via the Ashigaru mobile app (see Section 5 of its tutorial) or via Ashigaru Terminal (also detailed in Section 5 of its tutorial).

Once your Deposit account holds at least the amount required to join the smallest pool (plus service fees and a minimum to cover mining fees), you’re ready to initiate your first coinjoins.

![Image](/assets/img/ashigaru-whirlpool-guide/02.webp)

## Create the Tx0

When funds have arrived in your Deposit account and the transaction is confirmed, you can start the coinjoin process. In Ashigaru Terminal, open the`Wallets` menu, then choose your wallet. If it’s locked, you’ll be asked for your password and passphrase.

![Image](/assets/img/ashigaru-whirlpool-guide/03.webp)

Select the`Deposit` account.

![Image](/assets/img/ashigaru-whirlpool-guide/04.webp)

Go to the`UTXOs` menu.

![Image](/assets/img/ashigaru-whirlpool-guide/05.webp)

You’ll see all UTXOs in your Deposit account. Select the UTXOs you want to enter into coinjoin cycles.

For better privacy — and to avoid the Common Input Ownership Heuristic (CIOH) — it’s recommended to use a UTXOs from the same sources in each Tx0, you do not want accidentally associate your KYC and Non-KYC coins for example.

Press`ENTER` to select a UTXO: an asterisk`(*)` will appear next to it.

![Image](/assets/img/ashigaru-whirlpool-guide/06.webp)

Click`Mix Selected`.

![Image](/assets/img/ashigaru-whirlpool-guide/07.webp)

If you have a UTXO large enough to enter one of the available pools, choose the pool using the arrow keys. On this page, you’ll see your Tx0 details:
- Number of UTXOs entering the pool
- Fees applied (service fees and mining fees)
- Amount of doxxic change

Review the information carefully, then click`Broadcast` to send the Tx0.

![Image](/assets/img/ashigaru-whirlpool-guide/08.webp)

Ashigaru will then show the TXID of your Tx0, confirming the transaction was broadcast to the network.

![Image](/assets/img/ashigaru-whirlpool-guide/09.webp)

> Use UTXOs from similar sources when possible — combining multiple UTXOs can expose your ownership of said UTXOs via the CIOH.
{: .prompt-warning }

## Run the coinjoins

After broadcasting the Tx0, return to your Deposit account’s home page, click`Accounts`, and select the`Premix` account.

![Image](/assets/img/ashigaru-whirlpool-guide/10.webp)

In the`UTXOs` menu, you’ll see the equalized pieces ready to enter coinjoin cycles. As soon as the Tx0 confirms, Ashigaru Terminal can automatically start the first mixing cycle.

![Image](/assets/img/ashigaru-whirlpool-guide/11.webp)

Once the Tx0 is confirmed, the first coinjoin transaction will be created and broadcast automatically by Ashigaru Terminal. In`Accounts > Postmix > UTXOs`, you can view your equalized UTXOs waiting for their first cycle’s confirmation.

![Image](/assets/img/ashigaru-whirlpool-guide/12.webp)

You can now leave Ashigaru Terminal running in the background. It will continue mixing and remixing your coins automatically.

> Remixes continue automatically and cost nothing extra in Whirlpool (no additional service or mining fees for remixes).
{: .prompt-tip }

## Finishing the coinjoins

You can let your coins keep remixing automatically. In Whirlpool, remixes accrue no extra fees — neither service nor mining — so more cycles can only improve your privacy.

To better understand this mechanism and how many cycles are worthwhile, I will post a detailed article in the future, stay tuned!

To see how many remixes each coin has completed, open the`UTXOs` menu of the`Postmix` account.

![Image](/assets/img/ashigaru-whirlpool-guide/13.webp)

To spend your mixed coins, use the Ashigaru mobile app, which shares the same wallet as Ashigaru Terminal. The default view is the`Deposit` account. To access the`Postmix` account (where mixed UTXOs live), tap the Whirlpool icon at the top right.

![Image](/assets/img/ashigaru-whirlpool-guide/14.webp)

In this account, you’ll see all your coins in the mixing process. To spend, tap the`+` symbol at the bottom right, then select`Send`.

![Image](/assets/img/ashigaru-whirlpool-guide/15.webp)

Enter your transaction details: the recipient address, the amount, and — if you wish — select a specific transaction type for even stronger privacy.

![Image](/assets/img/ashigaru-whirlpool-guide/16.webp)

Carefully check the transaction information, then slide the arrow at the bottom of the screen to confirm sending.

![Image](/assets/img/ashigaru-whirlpool-guide/17.webp)

Your transaction has been signed and broadcast to the Bitcoin network.

![Image](/assets/img/ashigaru-whirlpool-guide/18.webp)

## Spending the doxxic change

Remember: Whirlpool’s model equalizes coins in the Tx0 before entering pools, which makes tracking harder. This is the most effective coinjoin model, but it has a drawback: a change output that does not go through the coinjoin process, we call this doxxic change.

This change output is created for each Tx0. It is isolated in a specific account named`Doxxic Change` or`Bad Bank` (depending on the software) to avoid using it with your other UTXOs. This point is critical: these UTXOs have not been mixed — their traceability links remain intact and can compromise your privacy by tying you to your coinjoin activity. Handle them carefully and never use them with other UTXOs, mixed or not. Combining a toxic UTXO with a mixed UTXO destroys all privacy gains from coinjoins. — Although using official wallets should never allow this to happen, easy tiger.

Currently, Ashigaru does not provide direct access to the`Doxxic Change` account (at least, it wasn’t found at the time of writing). This feature will likely be added in a future update. In the meantime, the only way to retrieve these funds is to import your seed into Sparrow Wallet. Sparrow usually auto‑detects a Whirlpool wallet and gives access to all four accounts, including`Doxxic Change`. You can then spend those UTXOs like regular bitcoin from Sparrow.

Here are several possible strategies to handle coinjoin change UTXOs without compromising your privacy:

- Mix them in smaller pools: If a toxic UTXO is large enough for a smaller pool, this is often the best option. Do not merge multiple toxic UTXOs to reach the threshold — that would link your entries.

- Mark them as unspendable: Another cautious approach is to keep them in their separate account and not touch them to avoid accidental spending. If BTC appreciates, new pools may become available for their size.

- Donate them: You can donate toxic UTXOs to Bitcoin developers, open‑source projects, or nonprofits that accept BTC. This disposes of them usefully while supporting the ecosystem.

- Buy gift cards or prepaid Visa cards: Platforms like [Bitrefill](https://www.bitrefill.com/) allow exchanging bitcoin for gift cards or reloadable Visa cards. This can be a simple, discreet way to spend toxic UTXOs. But be aware that these UTXOs are still linked to their previous history so be careful which ones you spend

- Swap them for Monero: Samourai Wallet previously offered atomic BTC/XMR swaps (now discontinued). This service exists in [Eigen Wallet](https://eigenwallet.org/), you can isolate these UTXOs, convert to XMR, then back to BTC if desired. This method can be costly and depends on available liquidity. Also you must consider you you want to potentially risk a UTXO that may be associated with you being given to a third party who can do whatever they want with it?

- Open a lightning channel: Transferring toxic UTXOs to LN to benefit from lower transaction fees can be useful. However, this may leak information depending on your LN usage, so proceed carefully.

> Carefully consider what you want to do with your doxxic change, always proceed carefully.
{: .prompt-danger }

## How to assess coinjoin quality

Effective coinjoins exhibit high homogeneity between input and output amounts. This uniformity increases the number of plausible interpretations for an external observer and therefore uncertainty about the transaction. We measure this uncertainty using entropy applied to the transaction. Whirlpool’s model is recognized as one of the strongest in this regard, thanks to excellent homogeneity between participants.

The performance across multiple coinjoin cycles is measured by the size of the sets in which a coin is hidden — called anonsets. There are two types:
- One measures privacy against retrospective analysis (from present to past)
- The other measures resistance against prospective analysis (from past to present)

For a full explanation of these two indicators a follow-up post will be posted.

## How to manage postmix

After several coinjoin cycles, the best strategy is to keep UTXOs in the`Postmix` account, letting them remix indefinitely until you actually need to spend them.

Some users prefer moving mixed BTC to a hardware wallet. This is possible, but it requires discipline to avoid compromising privacy gains from coinjoins.

The most common mistake is merging UTXOs. Never combine mixed UTXOs with unmixed UTXOs in the same transaction, or you risk creating links via the CIOH. This means rigorous UTXO management is key — clear and precise labeling is essential. In general, UTXO merging is risky and often leads to privacy loss when done poorly.

Be careful with consolidation of mixed UTXOs with each other, too. Limited consolidation may be acceptable if UTXOs have large anonsets, but it inevitably reduces your privacy. Avoid large or rushed consolidations before sufficient remixes, as they can create deducible links between your coins before and after mixing. When in doubt, do not consolidate postmix UTXOs. Instead, transfer them one by one to your hardware wallet, generating a fresh receiving address each time. Label every transferred UTXO carefully.

It is strongly discouraged to move postmix UTXOs into wallets using minority scripts. For example, if you participated in Whirlpool from a multisig`P2WSH` wallet, few users share that script type. Sending postmix UTXOs back to the same script greatly reduces your anonymity set. Beyond script type, other wallet fingerprints can harm your privacy. The safest option is to spend from the Ashigaru app.

Finally, as with any Bitcoin usage, never reuse a receiving address. Each payment should go to a fresh, unused address.

The simplest and safest method remains: keep mixed UTXOs resting in`Postmix`, let them remix naturally, and spend only when needed from Ashigaru.

Ashigaru and Sparrow include additional protections against common chain‑analysis pitfalls, helping you preserve transaction privacy.

> Avoid merging mixed and unmixed UTXOs; prefer spending from Postmix directly; don’t reuse addresses; and be cautious with script types and consolidations.
{: .prompt-warning }