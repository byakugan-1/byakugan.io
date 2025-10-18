---
title: BIP-39 Passphrases
date: 2024-10-19 12:30:00 +0000
categories: [Guide, Wallets]
tags: [bitcoin,sparrow,security,wallets,passphrase]     # TAG names should always be lowercase
description: Information on BIP-39 Passphrases
# comments: false
author: loicpandul
image: assets/img/passphrase-images/cover.webp
---

## What is a BIP39 passphrase?

HD wallets are typically generated from a mnemonic phrase consisting of 12 or 24 words. This phrase is very important because it allows for the restoration of all the keys of a wallet in case its physical medium (like a hardware wallet, for example) is lost. However, it constitutes a single point of failure because if it is compromised, an attacker could steal all the bitcoins.

![PASSPHRASE BIP39](assets/img/passphrase-images/01.webp)

This is where the passphrase comes in. It's an optional password that you can freely choose, which is added to the mnemonic phrase in the key derivation process to enhance the wallet's security.

![PASSPHRASE BIP39](assets/img/passphrase-images/02.webp)

> Do not confuse the BIP39 passphrase with a device PIN or software login password: the passphrase is part of key derivation. Without the exact passphrase, your bitcoins are unrecoverable. Changing it (even slightly) creates a different wallet with different keys.
{: .prompt-danger }

## Why should you use a passphrase?

The passphrase is arbitrary and can be any combination of characters chosen by the user. Using a passphrase thus offers several advantages. First, it reduces all risks associated with the compromise of the mnemonic phrase by requiring a second factor to access the funds (burglary, access to your home, etc.).

Next, it can be used strategically to create a decoy wallet, to deal with physical constraints to steal your funds like the infamous "*$5 wrench attack*". In this scenario, the idea is to have a wallet without a passphrase containing only a small amount of bitcoins, enough to satisfy a potential aggressor, while having a hidden wallet. This latter uses the same mnemonic phrase but is secured with an additional passphrase.

Finally, using a passphrase is interesting when one wishes to control the randomness of the HD wallet's seed generation.

## How to choose a good passphrase?

> Use a long, high‑entropy passphrase. As a 2025 baseline: at least 13 random characters including digits, lowercase/uppercase letters, and symbols (or 14+ if only letters). Prefer 20+ characters with mixed classes to future‑proof against brute‑force advances.
{: .prompt-tip }

According to [a study conducted by Trezor in 2019](https://blog.trezor.io/is-your-passphrase-strong-enough-d687f44c63af), an attacker with access to your seed and using a high-end GPU rented on AWS (NVIDIA Tesla V100) could test nearly 620 million passphrases for 1 dollar. As a rough estimate, with 2019 capabilities, a passphrase made of 12 random lowercase letters would cost on average **77 million dollars** to crack.

> Back up your passphrase like your mnemonic: write it on a physical medium (paper or metal), stored separately from the mnemonic phrase and in a different location. If you lose the passphrase, you lose access to your bitcoins; do not rely solely on memory.
{: .prompt-danger }