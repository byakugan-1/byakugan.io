---
title: Ashigaru Terminal Setup Guide
date: 2025-11-04 12:30:00 +0000
categories: [Guide, Wallets]
tags: [bitcoin,wallets,privacy,samourai,dojo,ashigaru,terminal]     # TAG names should always be lowercase
description: How to Use Ashigaru on desktop to perform Whirlpool coinjoins
# comments: false
# pin: true
authors: [vibrant, loicpandul]
image: /assets/img/ashigaru-terminal-setup-guide/header.png
---

Ashigaru Terminal is Ashigaru’s adaptation of Sparrow Server that implements the Whirlpool coinjoin protocol. It continues the work started by Samourai Wallet, especially around Whirlpool GUI, keeping the core principles of self‑custody and privacy preservation.

This software is a fork of Sparrow Server, modified and optimized for full integration with the Whirlpool ecosystem — the ZeroLink coinjoin protocol originally developed by the Samourai team.

Ashigaru Terminal runs in a minimalist TUI interface and can be deployed on a personal computer or a dedicated server. It lets you interact directly with Whirlpool to initiate Tx0 transactions, manage the Deposit, Premix, Postmix, and Badbank accounts, and perform automatic remixing to strengthen the privacy of your coins.

In short, Ashigaru Terminal is particularly useful if you want to perform coinjoins via Whirlpool.

In this first tutorial, we’ll handle installation and the basics of Ashigaru Terminal. A second, more advanced tutorial will then cover performing coinjoins in practice.

> Proper coinjoins improve privacy by breaking deterministic links between inputs and outputs.
{: .prompt-info }

## 1. Install Ashigaru Terminal

To install Ashigaru Terminal, you’ll need Tor Browser because binaries are distributed only over the Tor network. If you haven’t already, [install Tor Browser](https://www.torproject.org/download/) on your machine.

### 1.1. Download Ashigaru Terminal

From Tor Browser, go to the releases page of the Git repository and download the latest version for your operating system:

Onion link - http://ashicodepbnpvslzsl2bz7l2pwrjvajgumgac423pp3y2deprbnzz7id.onion/Ashigaru/Ashigaru-Terminal/releases/

![Image](/assets/img/ashigaru-terminal-setup-guide/01.webp)

Download the two files that match your operating system:
- The binary (for example:`ashigaru_terminal_v1.0.0_amd64.deb` for Debian/Ubuntu,`.dmg` for macOS, or`.zip` for Windows)
- The signed hashes file:`ashigaru_terminal_v1.0.0_signed_hashes.txt`

> Always download both the binary and the signed hashes file — you’ll need the latter to verify authenticity and integrity before running anything.
{: .prompt-warning }

### 1.2. Verify Ashigaru Terminal

Before running the software, verify its authenticity and integrity. This step helps you avoid installing a tampered version that could compromise your bitcoin or infect your machine.

Open a new tab and go to the [Keybase verification tool](https://keybase.io/verify). Paste the content of the`.txt` signed hashes file into the field, then click “Verify.”

![Image](/assets/img/ashigaru-terminal-setup-guide/02.webp)

To diversify verification sources, you can also compare the message with what’s on the clearnet site [ashigaru.rs](https://ashigaru.rs/download/) under the`/download` section.

![Image](/assets/img/ashigaru-terminal-setup-guide/03.webp)

If the signature is valid, Keybase will confirm the file was signed by the Ashigaru developers.

![Image](/assets/img/ashigaru-terminal-setup-guide/04.webp)

You can also click the “ashigarudev” profile shown by Keybase and ensure the key fingerprint matches exactly: A138 06B1 FA2A 676B.

![Image](/assets/img/ashigaru-terminal-setup-guide/05.webp)

> If the Keybase verification fails, do not install the software. Start the process over or ask the community for help before proceeding.
{: .prompt-danger }

Keybase gives you the authenticated hash for the app. Now verify the hash of the`.deb`,`.zip`, or`.dmg` you downloaded matches the Keybase-verified value. Use [HASH FILE ONLINE](https://hash-file.online/).

- Click “BROWSE...” and select the`.deb`,`.zip`, or`.dmg` you downloaded
- Choose SHA‑256 as the hashing function
- Click “CALCULATE HASH” to generate your file’s hash

![Image](/assets/img/ashigaru-terminal-setup-guide/06.webp)

The site will display the software hash. Compare it to the one verified on Keybase. If both match perfectly, authenticity and integrity checks have succeeded. You can now install/run the software.

![Image](/assets/img/ashigaru-terminal-setup-guide/07.webp)

> Never proceed if the signature or hash comparison fails — investigate the mismatch first.
{: .prompt-danger }

### 1.3. Launch Ashigaru Terminal

- Debian / Ubuntu

To install:

```bash
cd ~/Downloads
sudo apt install ./ashigaru_terminal_v1.0.0_amd64.deb
```
> Adjust the filename to match the version you downloaded.
{: .prompt-info }

Then verify the installation:

```bash
/opt/ashigaru-terminal/bin/Ashigaru-terminal --version
```
> Prints the installed version to confirm the binary is available.
{: .prompt-tip }

Launch the software:

```bash
/opt/ashigaru-terminal/bin/Ashigaru-terminal
```
> Starts the TUI app in your terminal.
{: .prompt-info }

- Windows

Right‑click the`.zip` file you downloaded and verified, then select “Extract All...” to unpack it.

Once extraction is complete, double‑click`Ashigaru-terminal.exe` to launch the app.

![Image](/assets/img/ashigaru-terminal-setup-guide/08.webp)

> On macOS, open the downloaded`.dmg` and follow the standard app installation process. Gatekeeper may require you to allow running apps from this developer.
{: .prompt-tip }

## 2. Get familiar with Ashigaru Terminal

Ashigaru Terminal uses a TUI (Text‑based User Interface), meaning a minimalist interface that runs directly in the terminal. You interact using menus and keyboard shortcuts without a traditional GUI.

![Image](/assets/img/ashigaru-terminal-setup-guide/09.webp)

Navigation is straightforward: use your keyboard’s arrow keys to move through menus, and press`Enter` to confirm or perform actions.

## 3. Connect your node to Ashigaru Terminal

By default, Ashigaru Terminal connects to a public Electrum server. This has obvious privacy and sovereignty drawbacks. Configure it to connect directly to your own Electrum server.

Open`Preferences > Server`.

![Image](/assets/img/ashigaru-terminal-setup-guide/10.webp)

Click`< Edit >`.

![Image](/assets/img/ashigaru-terminal-setup-guide/11.webp)

Select`Private Electrum Server`, then click`< Continue >`.

![Image](/assets/img/ashigaru-terminal-setup-guide/12.webp)

Enter your server’s URL and port. You can enter a Tor`.onion` address. Click`< Test >` to check the connection.

![Image](/assets/img/ashigaru-terminal-setup-guide/13.webp)

If the connection works, you’ll see`Success` and your server’s details.

![Image](/assets/img/ashigaru-terminal-setup-guide/14.webp)

> For best privacy, use your own Electrum server (onion if remote). Public servers can see and correlate wallet queries.
{: .prompt-warning }

To learn more about Dojo and how to install it, I have written the following dedicated guides:

[Vanilla Samourai Dojo Guide](https://byakugan.io/posts/vanilla-dojo-guide/)

[How to Run a Bitcoin Node (Advanced)](https://byakugan.io/posts/node-guide/)

Note: For this tutorial, the author disconnects from their because they’re on testnet. Operation is the same on mainnet.

## 4. Create a wallet in Ashigaru Terminal

With the software configured, add a Bitcoin wallet.

You have two choices:
- Create a brand‑new wallet to use exclusively with Ashigaru Terminal. You’ll need this app running whenever you want to interact with the wallet.
- Import your existing Ashigaru (mobile) wallet into Ashigaru Terminal. This slightly reduces security (one wallet across two environments), but it lets Ashigaru Terminal run continuously to mix coins while you spend from the mobile app.

In this tutorial, we’ll use the second option. If you prefer creating a new wallet, the procedure is identical — the only difference is you’ll back up the new mnemonic and BIP39 passphrase during creation.

Note: Ashigaru Terminal cannot spend directly. Either sync the same wallet in Terminal and the Ashigaru app (as we do here) or use`Mix to` (covered next tutorial) to auto‑send funds to a hardware wallet after a set number of mix cycles.

If you don’t yet have a wallet in the Ashigaru app, follow [this guide](https://byakugan.io/posts/ashigaru-mobile-guide).

Go to`Wallets`.

![Image](/assets/img/ashigaru-terminal-setup-guide/15.webp)

Select`Create / restore wallet...`. Use`Open Wallet...` later to reopen a wallet already saved in Ashigaru Terminal.

![Image](/assets/img/ashigaru-terminal-setup-guide/16.webp)

Give your wallet a name.

![Image](/assets/img/ashigaru-terminal-setup-guide/17.webp)

Choose`Hot Wallet` as the wallet type.

Note:`Watch‑only` lets you save a hardware wallet xpub for later use with`Mix to`. A watch‑only wallet cannot participate in coinjoins. We’ll cover “Mix to” in the next tutorial.

![Image](/assets/img/ashigaru-terminal-setup-guide/18.webp)

Now the flow differs based on your choice:
- To create a new wallet from scratch, click`< Generate New Wallet >`, set a BIP39 passphrase, and carefully back up the mnemonic and passphrase on physical media:

- To use the same wallet as your Ashigaru Mobile app, enter your 12‑word mnemonic and your BIP39 passphrase exactly: lowercase words, in order, separated by a single space, with no extra characters.

Click`< Next >` when done.

Note: If`< Next >` is disabled, your mnemonic is invalid. Double‑check for typos or incorrect words.

![Image](/assets/img/ashigaru-terminal-setup-guide/19.webp)

You’ll then set a password. This unlocks your wallet in Ashigaru Terminal and protects it from unauthorized physical access. It is not part of key derivation. In other words, even without this password, anyone with your mnemonic and passphrase can restore your wallet and access your funds.

Choose a long, random password. Store a copy safely — ideally on physical media or in a secure password manager.

Click`< OK >` when finished.

![Image](/assets/img/ashigaru-terminal-setup-guide/20.webp)

> Mnemonic + passphrase = full access. Treat the Terminal password as a local lock; it does not replace seed security.
{: .prompt-info }

## 5. Use the wallet

Choose which account to view. Since we haven’t started mixing yet, open the`Deposit` account.

![Image](/assets/img/ashigaru-terminal-setup-guide/21.webp)

Operation is similar to Sparrow (Ashigaru Terminal is a fork of Sparrow Server), so you’ll see familiar menus:

![Image](/assets/img/ashigaru-terminal-setup-guide/22.webp)

-`Transactions`: view transaction history for this account. In this example, some appear already because they were made with the Ashigaru Mobile app using the same wallet.

![Image](/assets/img/ashigaru-terminal-setup-guide/23.webp)

-`Receive`: generate a fresh receiving address to fund the deposit account.

![Image](/assets/img/ashigaru-terminal-setup-guide/24.webp)

-`Addresses`: list all addresses in this account (internal and external chains).

![Image](/assets/img/ashigaru-terminal-setup-guide/25.webp)

-`UTXOs`: list all available UTXOs.

![Image](/assets/img/ashigaru-terminal-setup-guide/26.webp)

-`Settings`: view the wallet descriptor, check the seed, adjust Gap Limit, or change the wallet birthday/start date.

![Image](/assets/img/ashigaru-terminal-setup-guide/27.webp)

You now know how to install and get started with Ashigaru Terminal. In the next tutorial, we’ll cover performing coinjoins and managing Postmix funds — either via the Ashigaru mobile app or with the`Mix to` option.