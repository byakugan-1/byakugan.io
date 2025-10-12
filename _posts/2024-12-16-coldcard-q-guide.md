---
title: Coldcard Q Guide
date: 2024-12-16 12:30:00 +0000
categories: [Guide, Wallets]
tags: [bitcoin,sparrow,security,wallets,hardware,coinkite,passphrase,coldcard]     # TAG names should always be lowercase
description: How to Setup and Use the Coldcard Q (beginner to expert)
# comments: false
authors: [loicpandul, vibrant]
---

![cover](/assets/img/coldcardq-guide-images/cover.webp)

A hardware wallet is an electronic device dedicated to managing and securing the private keys of a Bitcoin wallet. Unlike software wallets (or hot wallets) installed on general-purpose machines often connected to the Internet, hardware wallets enable private keys to be physically isolated, reducing the risk of piracy and theft.

The main objective of a hardware wallet is to reduce the device's functionality as much as possible in order to minimize its attack surface. Less attack surface also means fewer potential attack vectors, i.e. fewer weak points in the system that attackers could exploit to gain access to bitcoins.

It's advisable to use a hardware wallet to secure your bitcoins, especially if you hold large quantities, either in absolute value or as a proportion of your total assets.

Hardware wallets are used in conjunction with wallet management software on a computer or smartphone. The latter manages the creation of transactions, but the cryptographic signature required to make these transactions valid is performed solely within the hardware wallet. This means that private keys are never exposed to a potentially vulnerable environment.

Hardware wallets offer double protection for the user: on the one hand, they secure your bitcoins against remote attacks by keeping the private keys offline, and on the other, they generally offer greater physical resistance to attempts to extract the keys. And it is precisely on these 2 security criteria that we can judge and classify the different models on the market.

In this tutorial, I'd like to introduce you to one such solution: the COLDCARD Q.

---

As the COLDCARD Q offers a multitude of functions, this guide covers both the initial configuration and basic functions, followed by advanced options for expert users at the end of the document.

---

## Introducing COLDCARD Q

The COLDCARD Q is a Bitcoin-only hardware wallet developed by Canadian company Coinkite, known for its previous MK models. The Q represents their most advanced product to date, and is positioned as the ultimate Bitcoin hardware wallet.

In terms of hardware, the COLDCARD Q is equipped with all the features required for an optimal user experience:

- A large LCD display simplifies navigation and operation
- A full QWERTY keyboard
- Integrated camera for scanning QR codes
- Two microSD card slots
- A fully isolated power option via three AAA batteries (not included), or via a USB-C cable
- Two Secure Elements from two different manufacturers for added security
- The ability to communicate via NFC

In my opinion, the COLDCARD Q has a few drawbacks. Firstly, the coldcard is not truly FOSS (Free and Open Source Software) unlike the [Foundation Passport](https://foundation.xyz/passport-core/). Secondly, because of its many features, it's quite bulky, measuring almost 13 cm long and 8 cm wide, which is almost the size of a small smartphone. It's also rather thick because of the battery compartment. If you're looking for a smaller, more mobile hardware wallet, the much more compact MK4 might be a better option. Another drawback is obviously the cost of the device, which is priced at USD 239.99 on the official website, i.e. USD 72 more than the MK4, which puts the Q in direct competition with premium hardware wallets like the Ledger Flex or Foundation's Passport.

![Image](/assets/img/coldcardq-guide-images/001.webp)

On the software side, the COLDCARD Q is as well equipped as Coinkite's other devices, with a host of advanced features:

- Dice Roll to generate your own recovery phrase
- PIN code
- Countdown to final PIN lock
- BIP39 passphrase
- Final locking PIN
- Connection countdown
- SeedXOR
- BIP85

In short, the COLDCARD Q offers an improved user experience over the MK4, and may be ideal for intermediate to advanced users looking for greater ease of use.

The COLDCARD Q is available for sale [on the official Coinkite website](https://store.coinkite.com/store/coldcard). It can also be purchased from a retailer.

## Preparing the tutorial

Once you've received your COLDCARD, the first step is to inspect the packaging to make sure it hasn't been opened.

![Image](/assets/img/coldcardq-guide-images/002.webp)

> Damaged or previously opened packaging may indicate tampering or a counterfeit device — stop and contact the seller.
{: .prompt-danger }

When you open the box, you should find the following items:

- The COLDCARD Q in a sealed bag
- A card to record your mnemonic phrase

![Image](/assets/img/coldcardq-guide-images/003.webp)

![Image](/assets/img/coldcardq-guide-images/004.webp)

> Verify the bag is sealed and undamaged, confirm the bag’s number matches the card inside, and retain this number for future checks.
{: .prompt-warning }

If you prefer to power your COLDCARD without connecting it to a computer (air-gap), insert three AAA batteries into the back of the device. Alternatively, you can connect it to your computer via a USB-C cable.

![Image](/assets/img/coldcardq-guide-images/005.webp)

For this tutorial, you'll also need Sparrow Wallet to manage your Bitcoin wallet on your computer. Download [Sparrow Wallet](https://sparrowwallet.com/download/) from the official website. If you don't know how to do this, follow this [tutorial](https://byakugan.io/posts/gnupg-guide).

> Verify Sparrow’s signature and hash before installing to reduce supply‑chain and tampering risks.
{: .prompt-warning }

## PIN code selection

You can now switch on your COLDCARD by pressing the button in the top left-hand corner.

![Image](/assets/img/coldcardq-guide-images/006.webp)

Press the "ENTER" button to accept the terms of use.

![Image](/assets/img/coldcardq-guide-images/007.webp)

Your COLDCARD Q will then display a number at the top of the screen. Make sure this number matches the one on the sealed bag and on the piece of plastic inside the bag. This ensures that your package has not been opened between the time it was packaged by Coinkite and the time you opened it. Press "ENTER" to continue.

![Image](/assets/img/coldcardq-guide-images/008.webp)

Navigate to the "Choose PIN Code" menu and confirm with the "ENTER" button.

![Image](/assets/img/coldcardq-guide-images/009.webp)

This PIN code is used to unlock your COLDCARD. It is therefore a protection against unauthorized physical access. This PIN code is not involved in the derivation of your wallet's cryptographic keys. So, even without access to this PIN code, possession of your mnemonic phrase will enable you to regain access to your bitcoins.

COLDCARD PIN codes are divided into two parts: a prefix and a suffix, each of which can contain between 2 and 6 digits, for a total of 4 to 12 digits. When you unlock your COLDCARD, you'll first need to enter the prefix, after which the device will show you 2 words. Then enter the suffix. These two words will be given to you during this configuration step, and should be carefully saved, as you'll need them every time you unlock your COLDCARD.

> Always verify the two anti‑phishing words after entering your PIN prefix; if they differ from your saved words, stop and investigate.
{: .prompt-warning }

Click again on "Choose PIN"

![Image](/assets/img/coldcardq-guide-images/010.webp)

Confirm that you have read the warnings.

![Image](/assets/img/coldcardq-guide-images/011.webp)

You will now choose your PIN code. We recommend a long, random PIN code. Make sure you keep this code in a different place from where your COLDCARD is stored. You can use the card supplied in your parcel to record this code.

Enter the prefix of your choice, then press the "ENTER" button to confirm the first part of the PIN code.

![Image](/assets/img/coldcardq-guide-images/012.webp)

The two anti-phishing words will then be displayed on your screen. Save them carefully for future reference. You can use the card included in your package to write them down.

![Image](/assets/img/coldcardq-guide-images/013.webp)

Then enter the second part of your PIN code and press "ENTER".

![Image](/assets/img/coldcardq-guide-images/014.webp)

Confirm your PIN by entering it a second time, checking that the two anti-phishing words match those you saved earlier.

![Image](/assets/img/coldcardq-guide-images/015.webp)

## Firmware update

When using your device for the first time, it is important to check and update the firmware. To do this, access the "Advanced/Tools" menu.

![Image](/assets/img/coldcardq-guide-images/016.webp)

> If you already have a wallet on the device, ensure your mnemonic (and any passphrase) is backed up and tested before updating — a failed update without backups can mean permanent loss.
{: .prompt-danger }

Select "Upgrade Firmware".

![Image](/assets/img/coldcardq-guide-images/017.webp)

Select "Show Version".

![Image](/assets/img/coldcardq-guide-images/018.webp)

You can check the current firmware version of your COLDCARD. For example, in my case, the version is "1.2.3Q".

![Image](/assets/img/coldcardq-guide-images/019.webp)

Check [on the official COLDCARD website](https://coldcard.com/downloads) to see if a newer version is available. Click on "Download" to download the new firmware.

![Image](/assets/img/coldcardq-guide-images/020.webp)

At this point, download [the document containing the hashes of all versions, signed by the developers](https://raw.githubusercontent.com/Coldcard/firmware/master/releases/signatures.txt), verify the signature with [the developer's public key](https://keybase.io/dochex), and make sure that the hash indicated in the signed document matches that of the firmware downloaded from the site. If everything is correct, you can proceed with the update.

If you are not familiar with this verification process, I recommend you follow this [tutorial](https://byakugan.io/posts/gnupg-guide).

> Verify firmware signatures and hashes; never install unverified firmware.
{: .prompt-warning }

Take a microSD card and transfer the firmware file (document in .dfu) to it. Insert the microSD card into one of your COLDCARD's ports.

![Image](/assets/img/coldcardq-guide-images/021.webp)

Then, in the firmware update menu, select "From MicroSD".

![Image](/assets/img/coldcardq-guide-images/022.webp)

Select the file corresponding to the firmware.

![Image](/assets/img/coldcardq-guide-images/023.webp)

Confirm your selection by pressing the "ENTER" button.

![Image](/assets/img/coldcardq-guide-images/024.webp)

Please wait while the firmware is updated.

![Image](/assets/img/coldcardq-guide-images/025.webp)

Once the update is complete, enter your PIN code to unlock the device.

![Image](/assets/img/coldcardq-guide-images/026.webp)

Your firmware is now up to date.

## COLDCARD Q parameters

If you wish, you can explore your COLDCARD's settings by accessing the "Settings" menu.

![Image](/assets/img/coldcardq-guide-images/027.webp)

In this menu, you'll find various customization options, such as setting the screen brightness or selecting the default unit of measurement.

![Image](/assets/img/coldcardq-guide-images/028.webp)

We'll look at other advanced settings later in the expert section.

## Creating a Bitcoin wallet

Now it's time to generate a new Bitcoin wallet! To do this, you need to create a mnemonic phrase. On COLDCARD, you have three methods for generating this phrase:

- Use only the internal random number generator (TRNG)
- Use a combination of TRNG and dice rolling to add entropy
- Use dice rolls only

For beginners and intermediate users, we recommend using only the COLDCARD's internal random number generator

I don't recommend the dice-only option, as poor execution can lead to a sentence with insufficient entropy, jeopardizing the security of your wallet.

However, the best option is surely the second, which combines the use of TRNG with an external entropy source. This method guarantees a minimum entropy equivalent to that of TRNG alone, and adds an extra level of security in the event of a possible failure of the internal generator (voluntary or not). By choosing this option, which combines TRNG and dice rolling, you benefit from greater control over the generation of the sentence, without increasing the risks in the event of poor execution on your part.

Click on "New Seed Words".

![Image](/assets/img/coldcardq-guide-images/029.webp)

You can choose the length of your sentence. I recommend that you opt for a 12-word sentence, as it's less complex to manage and offers no less wallet security than a 24-word sentence.

![Image](/assets/img/coldcardq-guide-images/030.webp)

The COLDCARD will then display your TRNG-generated recovery phrase. If you wish to add additional external entropy, press the "4" key.

![Image](/assets/img/coldcardq-guide-images/031.webp)

This will take you to a page where you can add entropy by rolling the dice. Make as many throws as possible (a minimum of 50 is recommended, but less is not a big deal as you're already benefiting from the entropy of the TRNG), and record the results with the "1" to "6" keys. When finished, press "ENTER" to confirm.

![Image](/assets/img/coldcardq-guide-images/032.webp)

A new mnemonic phrase will be displayed, based on the entropy you've just provided and that of the TRNG.

> Your 12 words grant full, unrestricted access to your bitcoin. Never digitize them (no photos, files, cloud). Anyone with the words can steal your funds.
{: .prompt-danger }

You can write it down on the cardboard supplied with your COLDCARD, or for added security, I recommend that you engrave it on a stainless steel support to protect it from the risk of fire, flood or collapse. In any case, never save it on a digital medium, otherwise you could lose your bitcoins.

Write down the words provided on the screen on the physical medium of your choice. Depending on your security strategy, you may consider making several complete physical copies of the sentence (but above all, don't split it up). It's important to keep the words numbered and in sequential order.

Obviously, you must never share these words on the Internet, unlike in this tutorial. This sample wallet will be used only on Testnet and will be deleted at the end of the tutorial.

After writing down the words, press "ENTER".

![Image](/assets/img/coldcardq-guide-images/033.webp)

To make sure you've saved your phrase correctly, the system will ask you to confirm certain words. Select the number corresponding to each word on the keypad.

![Image](/assets/img/coldcardq-guide-images/034.webp)

Your wallet is now created! In the top right-hand corner of the screen, you can see your master private key fingerprint. Press "ENTER".

![Image](/assets/img/coldcardq-guide-images/035.webp)

You now access your COLDCARD's main menu.

![Image](/assets/img/coldcardq-guide-images/036.webp)

## Setting up a new wallet on Sparrow

There are several options for establishing communication between the Sparrow Wallet software and your COLDCARD. The most straightforward is to use a USB-C cable. However, by default, your COLDCARD has cable and NFC communications disabled. To reactivate them, navigate to "Settings", then "Hardware On/Off", and activate the desired communication option.

![Image](/assets/img/coldcardq-guide-images/037.webp)

If you prefer to keep your COLDCARD totally isolated from your computer, you can opt for indirect "air-gap" communication, using QR codes or a microSD card. This is the method we'll be using in this tutorial.

Go to "Advanced/Tools".

![Image](/assets/img/coldcardq-guide-images/038.webp)

Select "Export Wallet".

![Image](/assets/img/coldcardq-guide-images/039.webp)

Then select "Sparrow Wallet".

![Image](/assets/img/coldcardq-guide-images/040.webp)

Press "ENTER" to generate the configuration file.

![Image](/assets/img/coldcardq-guide-images/041.webp)

Then choose how to send this file to Sparrow. In this example, I've inserted a microSD in slot "A", so I'll select the "1" button. You can also display the information as a QR code on your COLDCARD screen by pressing the "QR" button, and scan this QR code with your computer's webcam.

![Image](/assets/img/coldcardq-guide-images/042.webp)

Launch Sparrow Wallet and skip the introductory pages to reach the main screen. Make sure you are correctly connected to a node by checking the switch at the bottom right of the screen.

![Image](/assets/img/coldcardq-guide-images/043.webp)

> Prefer your own node (Bitcoin Core locally or your Electrum server). Public nodes reduce privacy and add trust assumptions.
{: .prompt-warning }

Access the "File" menu and select "New Wallet".

![Image](/assets/img/coldcardq-guide-images/044.webp)

Name your wallet and click on "Create Wallet".

![Image](/assets/img/coldcardq-guide-images/045.webp)

In the "Script Type" drop-down menu, choose the type of script that will secure your bitcoins.

![Image](/assets/img/coldcardq-guide-images/046.webp)

Click on "Airgapped Hardware Wallet".

![Image](/assets/img/coldcardq-guide-images/047.webp)

Under the "Coldcard" tab, click on "Scan..." if you plan to scan the QR code displayed on your COLDCARD, or "Import File..." to import the file from the microSD (this is the .json file).

![Image](/assets/img/coldcardq-guide-images/048.webp)

After import, confirm the creation by clicking on "Apply".

![Image](/assets/img/coldcardq-guide-images/049.webp)

> Verify Sparrow’s “Master fingerprint” matches the one shown on COLDCARD; a mismatch indicates a different wallet.
{: .prompt-warning }

Set up a strong password to secure access to your Sparrow Wallet. This password will protect your public keys, addresses, tags and transaction history from unauthorized access.

It's a good idea to save this password so you don't forget it (e.g. in a password manager).

![Image](/assets/img/coldcardq-guide-images/050.webp)

Your wallet is now set up on Sparrow Wallet.

![Image](/assets/img/coldcardq-guide-images/051.webp)

Before you receive your first bitcoins in your wallet, I strongly advise you to perform an empty recovery test. Write down some reference information, such as your xpub, then reset your COLDCARD Q while the wallet is still empty. Then try restoring your wallet to the COLDCARD using your paper backups. Check that the xpub generated after the restore matches the one you originally wrote down. If it does, you can rest assured that your paper backups are reliable.

To learn more about how to perform a recovery test, I suggest you consult this [tutorial](https://planb.network/tutorials/wallet/backup/recovery-test-5a75db51-a6a1-4338-a02a-164a8d91b895).

> Do a test restore before depositing funds to ensure your backups can recover the same wallet (match xpub/fingerprint).
{: .prompt-tip }

## Receive bitcoins

To receive your first bitcoins, start by switching on and unlocking your COLDCARD.

![Image](/assets/img/coldcardq-guide-images/052.webp)

On Sparrow Wallet, click on the "Receive" tab.

![Image](/assets/img/coldcardq-guide-images/053.webp)

Before using the address proposed by Sparrow Wallet, check it on your COLDCARD screen. This practice allows you to confirm that the address displayed on Sparrow is not fraudulent, and that the hardware wallet does indeed hold the private key needed to subsequently spend the bitcoins secured with this address. This helps you to avoid several types of attack.

> Always verify receive addresses on COLDCARD (Address Explorer or QR scan) before using them; wrong addresses mean irreversible loss.
{: .prompt-warning }

To perform this check, click on the "Address Explorer" menu on the COLDCARD.

![Image](/assets/img/coldcardq-guide-images/054.webp)

Select the type of script you are using on Sparrow. In my case, it's Segwit P2WPKH. I click on it.

![Image](/assets/img/coldcardq-guide-images/055.webp)

You can then see your different derived addresses in order.

![Image](/assets/img/coldcardq-guide-images/056.webp)

Check with Sparrow that the address matches. In my case, the address with derivation path `m/84'/1'/0'/0/0` is indeed `tb1qwfwwvzssep4wyjg3vsgezmwa037ehvd4fhmjvr` on both Sparrow and COLDCARD.

![Image](/assets/img/coldcardq-guide-images/057.webp)

Another way to verify ownership of this address is to scan its QR code directly onto Sparrow from your COLDCARD. From your COLDCARD home screen, select "Scan Any QR Code". You can also use the "QR" key on the keyboard.

![Image](/assets/img/coldcardq-guide-images/058.webp)

Scan the QR code of the address displayed on Sparrow Wallet.

![Image](/assets/img/coldcardq-guide-images/059.webp)

Make sure the address displayed on your COLDCARD matches the one shown on Sparrow. Then press the "1" button.

![Image](/assets/img/coldcardq-guide-images/060.webp)

The address is thus successfully confirmed.

![Image](/assets/img/coldcardq-guide-images/061.webp)

You can now add a "Label" to describe the source of bitcoins that will be secured with this address. This is a good practice that allows you to better manage your UTXOs.

![Image](/assets/img/coldcardq-guide-images/062.webp)

For more information on labeling, I also recommend this other [tutorial](https://planb.network/tutorials/privacy/on-chain/utxo-labelling-d997f80f-8a96-45b5-8a4e-a3e1b7788c52).

You can then use this address to receive bitcoins.

![Image](/assets/img/coldcardq-guide-images/063.webp)

## Send bitcoins

Now that you've received your first sats in your COLDCARD-secured wallet, you can spend them too!

As always, start by switching on and unlocking your COLDCARD Q, then open Sparrow Wallet and navigate to the "Send" tab to prepare a new transaction.

![Image](/assets/img/coldcardq-guide-images/064.webp)

If you wish to "coin control", i.e. choose specifically which UTXOs to consume in the transaction, go to the "UTXOs" tab. Select the UTXOs you wish to spend, then click on "Send Selected". You will be redirected to the same screen in the "Send" tab, but with your UTXOs already selected for the transaction.

![Image](/assets/img/coldcardq-guide-images/065.webp)

Enter the destination address. You can also enter multiple addresses by clicking on the "+ Add" button.

![Image](/assets/img/coldcardq-guide-images/066.webp)

Write down a "Label" to remember the purpose of this expense.

![Image](/assets/img/coldcardq-guide-images/067.webp)

Select the amount to be sent to this address.

![Image](/assets/img/coldcardq-guide-images/068.webp)

Adjust the fee rate of your transaction according to the current market.

![Image](/assets/img/coldcardq-guide-images/069.webp)

Make sure all your transaction parameters are correct, then click on "Create Transaction".

![Image](/assets/img/coldcardq-guide-images/070.webp)

If everything is to your satisfaction, click on "Finalize Transaction for Signing".

![Image](/assets/img/coldcardq-guide-images/071.webp)

> Before signing, verify on COLDCARD the destination address, amount, and fee. A wrong address or amount cannot be reversed.
{: .prompt-warning }

Once you've built your transaction in Sparrow, it's time to sign it with your COLDCARD. To transmit the PSBT (unsigned transaction) to your device, you have several options. If wired data transmission is enabled, you can send the transaction directly via a USB-C connection, just as you would with any other hardware wallet. In this case, on Sparrow, you'd have to click on the "Sign" button in the bottom right-hand corner. In my example, this button is blocked because the COLDCARD is not connected by cable.

![Image](/assets/img/coldcardq-guide-images/072.webp)

If you prefer to maintain an "air-gap" connection without direct contact between the hardware wallet and your computer, you have 2 options. The first, and more complex, is to use a microSD card. Insert the microSD card into your computer, record the transaction via the "Save Transaction" button on Sparrow, then transfer this card to a port on your COLDCARD.

![Image](/assets/img/coldcardq-guide-images/073.webp)

Then access the "Ready To Sign" menu.

![Image](/assets/img/coldcardq-guide-images/074.webp)

Review the transaction details on your COLDCARD, including the receiving address, the amount sent and the transaction fee.

![Image](/assets/img/coldcardq-guide-images/075.webp)

If everything is correct, press the "ENTER" button to sign the transaction.

![Image](/assets/img/coldcardq-guide-images/076.webp)

Then place the microSD back in your computer and on Sparrow, click on "Load Transaction" to load the signed transaction from the microSD. You'll then be able to perform a final check before uploading it to the Bitcoin network.

![Image](/assets/img/coldcardq-guide-images/077.webp)

The second method of signing with your COLDCARD in Air-Gap, which is much simpler than the microSD method, is to scan the PSBT directly via the device's camera. On Sparrow, select "Show QR".

![Image](/assets/img/coldcardq-guide-images/078.webp)

On the COLDCARD, select "Scan Any QR Code". You can also use the "QR" key on the keyboard.

![Image](/assets/img/coldcardq-guide-images/079.webp)

Use the COLDCARD's camera to scan the QR code displayed on Sparrow.

![Image](/assets/img/coldcardq-guide-images/080.webp)

The transaction details will appear again for verification. Press "ENTER" to sign if all is to your satisfaction.

![Image](/assets/img/coldcardq-guide-images/081.webp)

Your COLDCARD will then display the signed transaction as a QR code. Use your computer's webcam to scan this QR code by selecting "Scan QR" on Sparrow.

![Image](/assets/img/coldcardq-guide-images/082.webp)

Your signed transaction is now visible on Sparrow. Check one last time that everything is correct, then click on "Broadcast Transaction" to broadcast it on the Bitcoin network.

![Image](/assets/img/coldcardq-guide-images/083.webp)

You can track your transaction in Sparrow Wallet's "Transactions" tab.

![Image](/assets/img/coldcardq-guide-images/084.webp)

Congratulations, you're now up to speed on the basic use of COLDCARD Q with Sparrow Wallet!

---

## Advanced options (Expert)

{% include embed/youtube.html id='6L2hhT0J27s' %}

In a previous tutorial, we covered the initial configuration of the COLDCARD Q and its basic functions for beginners. If you've just received your COLDCARD Q and haven't set it up yet, start with the general section above.

This expert section is dedicated to COLDCARD Q's advanced options, designed for advanced and paranoid users. COLDCARDs are distinguished from other hardware wallets by their many advanced security features. Of course, you don't have to use all these options. Just choose the ones that suit your security strategy.

> Misconfiguring advanced options can cause fund loss or permanently destroy your device. Proceed only with tested backups and clear understanding.
{: .prompt-danger }

Before you start, make sure you have access to a physical backup of your 12- or 24-word mnemonic phrase, and check its validity via the following menu: `Advanced/Tools > Danger Zone > Seed Functions > View Seed Words`.

![Image](/assets/img/coldcardq-expert-guide-images/01.webp)

> Verify your seed (and any passphrase) is correctly recorded and recoverable before changing critical settings.
{: .prompt-tip }

### The BIP39 passphrase

If you don't know what a BIP39 passphrase is, or if it's not entirely clear to you how it works, I strongly recommend that you take a look at this [tutorial](https://byakugan.io/posts/passphrases) beforehand, which covers the theoretical bases needed to understand the risks associated with using a passphrase.

Bear in mind that once you've set up the passphrase on your wallet, your mnemonic alone won't be enough to regain access to your bitcoins. You'll need both the mnemonic and the passphrase. What's more, you'll need to enter the passphrase every time you unlock your COLDCARD Q. This enhances security by making physical access to the COLDCARD and knowledge of the PIN insufficient without the passphrase.

On COLDCARDs, you have two options for managing your passphrase:

1. Classic entry: you enter the passphrase manually each time you use your hardware wallet, just as you do with other hardware wallets. COLDCARD Q simplifies this task with its full keyboard.
2. Encrypt and store the passphrase on a microSD card: insert the microSD into the COLDCARD Q each time you use it.

> A stored passphrase on microSD is convenience, not a backup. Always maintain a separate physical backup; losing the passphrase locks you out permanently.
{: .prompt-warning }

To set your BIP39 passphrase, access the "Passphrase" menu.

![Image](/assets/img/coldcardq-expert-guide-images/02.webp)

Enter your passphrase using the keyboard.

![Image](/assets/img/coldcardq-expert-guide-images/03.webp)

Once you've set your passphrase, COLDCARD Q will show you the master key fingerprint of the new wallet associated with this passphrase. Be sure to save this fingerprint. When you re-enter your passphrase when you use your device in the future, you'll be able to check that the fingerprint displayed matches the one you saved. This check ensures that you haven't made a mistake when entering your passphrase.

![Image](/assets/img/coldcardq-expert-guide-images/04.webp)

> Record the fingerprint for your passphrase‑applied wallet and verify it on each use to catch entry mistakes.
{: .prompt-tip }

You can now press "ENTER" to apply this passphrase to your mnemonic phrase and activate the new wallet. If you prefer to save this passphrase on a microSD, insert the card in the appropriate slot and press "1".

![Image](/assets/img/coldcardq-expert-guide-images/05.webp)

Your passphrase is now applied. The key imprint appears on the home screen and at the top of the screen.

![Image](/assets/img/coldcardq-expert-guide-images/06.webp)

Each time you unlock your COLDCARD Q, you'll need to access the "Passphrase" menu and enter your passphrase in the same way as above, to apply it to the mnemonic stored in the device and access the correct Bitcoin wallet.

![Image](/assets/img/coldcardq-expert-guide-images/07.webp)

If you've saved the passphrase on a microSD card, each time you use it, insert it into the COLDCARD and access the "Passphrase" menu. Your COLDCARD will load the passphrase directly from the microSD, so you won't need to enter it manually. Click on "Restore Saved".

![Image](/assets/img/coldcardq-expert-guide-images/08.webp)

Check that the length and first letter of the loaded passphrase are correct.

![Image](/assets/img/coldcardq-expert-guide-images/09.webp)

Confirm that the fingerprint displayed matches that of your wallet and click on "Restore".

![Image](/assets/img/coldcardq-expert-guide-images/10.webp)

Keep in mind that using a passphrase means you'll need to import a new set of keys derived from the combination of your mnemonic phrase and passphrase into your wallet management software (like Sparrow Wallet).

### Unlocking options

COLDCARDs also benefit from a host of options for the device unlocking process. Let's find out more about these advanced options.

#### Trick PINs

A Trick PIN is a secondary PIN code distinct from the one defined during initial device configuration. This code is used to trigger specific pre-configured actions as soon as it is entered when the COLDCARD is switched on. You can configure several Trick PINs, each linked to a different action. These features enable you to tailor your COLDCARD to your personal security strategy. They are particularly useful in cases of physical duress, such as during a robbery (commonly referred to in the Bitcoin community as a "USD 5 wrench attack").

To activate a Trick PIN and associate it with an action, access the `Settings > Login Settings > Trick PINs` menu.

![Image](/assets/img/coldcardq-expert-guide-images/11.webp)

Select "Add New Trick".

![Image](/assets/img/coldcardq-expert-guide-images/12.webp)

Set the PIN code to be associated with the action and remember to save it.

![Image](/assets/img/coldcardq-expert-guide-images/13.webp)

Then choose the action to be performed automatically each time you enter this Trick PIN. Here's the list of actions available for a PIN:

- "Brick Self": destroys both COLDCARD Q chips if the Trick PIN is entered, rendering the device totally unusable. It will then be impossible to resell, reuse or even return it to Coinkite. The device will become irretrievably obsolete.

![Image](/assets/img/coldcardq-expert-guide-images/14.webp)

- "Wipe Seed": offers several actions for deleting the seed, i.e. resetting the COLDCARD without destroying it. Unlike the "Brick Self" option, it will be possible to reconfigure the device using a backup of your mnemonic phrase. Options:
  - "Wipe & Reboot": removes the seed and reboots without displaying information
  - "Silent Wipe": silently wipes the seed and unlocks a random fake wallet
  - "Wipe -> Wallet": wipes discreetly and unlocks a pre-configured secondary bait wallet
  - "Say Wiped, Stop": deletes seed and displays `Seed is wiped, Stop`

![Image](/assets/img/coldcardq-expert-guide-images/15.webp)

- "Duress Wallet": the Trick PIN unlocks a wallet derived from the seed using BIP85. Designed as bait: the device acts as if it were the real wallet, but without the master PIN the attacker never accesses the real one.

![Image](/assets/img/coldcardq-expert-guide-images/16.webp)

- "Login Countdown": actions with a countdown before execution:
  - "Wipe & Countdown": clears the seed, starts a one-hour countdown
  - "Countdown & Brick": starts a one-hour countdown, then destroys chips permanently
  - "Just Countdown": a one-hour countdown, then restart; seed remains intact

![Image](/assets/img/coldcardq-expert-guide-images/17.webp)

- "Look Blank": makes the device appear empty as if the seed were deleted; seed remains intact.

![Image](/assets/img/coldcardq-expert-guide-images/18.webp)

- "Just Reboot": reboots the device; no other action.

![Image](/assets/img/coldcardq-expert-guide-images/19.webp)

- "Delta Mode": advanced duress countermeasure. Grants access to the real wallet for credibility, but blocks PSBT signing and disables seed viewing. Any attempt to retrieve the seed will delete it. The Trick PIN used with Delta Mode must share the same prefix as the real PIN (to display the same anti-phishing words) but have a different suffix.

![Image](/assets/img/coldcardq-expert-guide-images/20.webp)

> Some Trick PIN actions permanently erase seeds or brick the device. Test carefully on a blank device and ensure backups before enabling.
{: .prompt-danger }

Once you have selected an action, confirm your choice.

![Image](/assets/img/coldcardq-expert-guide-images/21.webp)

You can then view all configured Trick PINs in the dedicated menu.

![Image](/assets/img/coldcardq-expert-guide-images/22.webp)

By selecting an existing Trick PIN, you can check the associated action. You can also hide it with "Hide Trick", delete it with "Delete Trick", or change the PIN while retaining the associated action with "Change PIN".

![Image](/assets/img/coldcardq-expert-guide-images/23.webp)

The "Add If Wrong" option lets you configure a specific action that is automatically triggered after a certain number of incorrect attempts to enter the master PIN code.

> Delta Mode requires the Trick PIN to share the same prefix as the real PIN to keep anti‑phishing words identical; otherwise it reveals duress mode.
{: .prompt-warning }

#### Scramble Keys

This option scrambles the digits displayed on your keypad buttons when entering your PIN code, protecting your privacy even under camera surveillance.

`Settings > Login Settings > Scramble Keys`

![Image](/assets/img/coldcardq-expert-guide-images/24.webp)

Select "Scramble Keys".

![Image](/assets/img/coldcardq-expert-guide-images/25.webp)

From now on, the keypad digits are assigned randomly on each unlock.

![Image](/assets/img/coldcardq-expert-guide-images/26.webp)

#### Login Countdown

Impose a systematic countdown each time you attempt to unlock your COLDCARD. Useful to delay access (e.g., theft scenarios) or to force a delay before signing.

`Settings > Login Settings > Login Countdown`

![Image](/assets/img/coldcardq-expert-guide-images/27.webp)

Select the countdown time (e.g., 1 hour).

![Image](/assets/img/coldcardq-expert-guide-images/28.webp)

Each unlock attempt prompts for PIN…

![Image](/assets/img/coldcardq-expert-guide-images/29.webp)

…then waits for the set duration.

![Image](/assets/img/coldcardq-expert-guide-images/30.webp)

At the end of the countdown, enter PIN again to access the device.

![Image](/assets/img/coldcardq-expert-guide-images/31.webp)

> Do not confuse Login Countdown (delays normal unlock) with Trick PIN “Countdown & Brick” (destroys device after countdown).
{: .prompt-info }

#### Calculator Login

Disguise your COLDCARD as a working calculator on boot.

`Settings > Login Settings > Calculator Login`

![Image](/assets/img/coldcardq-expert-guide-images/32.webp)

Activate the option.

![Image](/assets/img/coldcardq-expert-guide-images/33.webp)

A calculator appears at startup.

![Image](/assets/img/coldcardq-expert-guide-images/34.webp)

For example, calculate the SHA256 of "Plan B Network".

![Image](/assets/img/coldcardq-expert-guide-images/35.webp)

To unlock: enter PIN prefix followed by a dash (e.g., `00-`), confirm anti-phishing words…

![Image](/assets/img/coldcardq-expert-guide-images/36.webp)

…then enter full PIN separated by a space or dash (e.g., `00 00`) to exit calculator mode and unlock.

![Image](/assets/img/coldcardq-expert-guide-images/37.webp)

### Cleanly destroying your COLDCARD

If disposing of your COLDCARD Q, destroy it correctly to ensure no wallet data can be recovered by a third party. Always confirm you have functional backups (mnemonic and any passphrase) and access to funds on another device before proceeding.

Three levels:

1. Delete only the seed (device remains usable):  
   `Advanced/Tools > Danger Zone > Seed Functions > Destroy Seed`

![Image](/assets/img/coldcardq-expert-guide-images/38.webp)

2. Permanently disable secure chips via software ("Brick Me" PIN): device becomes unusable forever

3. Physical destruction: drill the two secure chips (near "SHOOT HERE").  
   Precautions: remove batteries and unplug power, wait a few minutes, wear gloves and goggles.

![Image](/assets/img/coldcardq-expert-guide-images/39.webp)

> Physical destruction is irreversible. Remove power, use protective gear, and never reconnect a drilled device.
{: .prompt-danger }

Once the chips have been punched, do not reconnect the device.

Congratulations, you're now up to speed on COLDCARD Q's advanced options!