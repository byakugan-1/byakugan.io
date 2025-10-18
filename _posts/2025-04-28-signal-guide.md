---
title: Signal Encrypted Messaging Guide
date: 2025-04-28 12:30:00 +0000
categories: [Guide, Security]
tags: [messages,signal,encryption]     # TAG names should always be lowercase
description: Using Signal for end-to-end encrypted messaging
# comments: false
author: loicpandul
image: /assets/img/signal-guide-images/cover.webp
---

Signal is an end-to-end encrypted messaging application, designed to offer good privacy by default. Every message, call and file is protected by the Signal protocol, recognized as one of the most robust messaging protocols. It is reused by many other applications, including WathsApp, Facebook Messenger, Skype and Google Messages for RCS communications.

Signal was launched in 2014 by Moxie Marlinspike (pseudonym) and developed since 2018 by the Signal Foundation, a non-profit organization created with the support of Brian Acton (co-founder of WhatsApp).

![Image](/assets/img/signal-guide-images/01.webp)

Compared with WhatsApp, Signal stands out for its transparency: the application's code, both client- and server-side, is entirely open source. This allows anyone to audit it, and in particular to check that encryption is applied as advertised.

However, Signal relies on the use of a telephone number, which is its main weakness when it comes to anonymity compared with other solutions. Despite this, the application is, in my opinion, one of the most reliable in terms of security and privacy, thanks to its entirely open architecture and a widely adopted encryption protocol, and therefore tested and audited, unlike other more marginal applications.

| Application          | E2EE 1:1       | E2EE groups    | Anonymous registration | Open-source client license | Open-source server license | Decentralized server | Year of creation  |
| --- | --- | --- | --- | --- | --- | --- | --- |
| WhatsApp             | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ | 2009 |
| WeChat               | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | 2011 |
| Facebook Messenger   | ✅ | 🟡 (optional) | ❌ | ❌ | ❌ | ❌ | 2011 |
| Telegram             | 🟡 (optional) | ❌ | 🟡 | ✅ | ❌ | ❌ | 2013 |
| LINE                 | ✅ | ✅ | ❌ | ❌ | ❌ | ❌ | 2011 |
| Signal               | ✅ | ✅ | ❌ | ✅ | ✅ | ❌ | 2014 |
| Threema              | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ | 2012 |
| Element (Matrix)     | ✅ | ✅ | ✅ | ✅ | ✅ | 🟡 (federated) | 2016 |
| Delta Chat           | ✅ | ✅ | ✅ | ✅ | N/A | 🟡 (via email) | 2017 |
| Conversations (XMPP) | ✅ | ✅ | ✅ | ✅ | ✅ | 🟡 (federated) | 2014 |
| Session              | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | 2020 |
| SimpleX              | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | 2021 |
| Olvid                | ✅ | ✅ | ✅ | ✅ | ❌ | 🟡(no directory) | 2019 |
| Keet                 | ✅ | ✅ | ✅ | ❌ | N/A | ✅ | 2022 |
| Jami                 | ✅ | ✅ | ✅ | ✅ | N/A | ✅ | 2005 |
| Briar                | ✅ | ✅ | ✅ | ✅ | N/A | ✅ | 2018 |
| Tox                  | ✅ | ✅ | ✅ | ✅ | N/A | ✅ | 2013 |

E2EE = End-to-end encryption

## Install the Signal application

Signal is available on all platforms. You can download the application directly from your phone's application store:

- [Google Play](https://play.google.com/store/apps/details?id=org.thoughtcrime.securesms)
- [App Store](https://apps.apple.com/us/app/signal-private-messenger/id874139669)

On Android, it is also possible to [install via APK](https://github.com/signalapp/Signal-Android/releases).

In this tutorial, we'll concentrate on the mobile version, but please note that [desktop versions are also available](https://signal.org/fr/download/) (MacOS, Linux and Windows). You'll need to set up the mobile application first, however, before you can synchronize your account with the desktop version.

## Create an account on Signal

When you launch the application for the first time, click on the "Continue" button.

![Image](/assets/img/signal-guide-images/02.webp)

Enter your phone number, then click on "Next".

![Image](/assets/img/signal-guide-images/03.webp)

A verification code will be sent to you by SMS. Enter this code in the Signal application.

![Image](/assets/img/signal-guide-images/04.webp)

Choose a PIN code to secure your Signal account. This code encrypts your data, and can be used to restore access to your account if your device is lost. So it's important to choose a robust PIN code that's as long and random as possible, and to keep a reliable record of it.

![Image](/assets/img/signal-guide-images/05.webp)

Confirm this PIN code a second time.

![Image](/assets/img/signal-guide-images/06.webp)

You can now personalize your user profile. Choose a photo, enter your name or a nickname. At this stage, you can also define who can find you on Signal via your number. Select "Everyone" if you want to be visible, or "Nobody" to remain untraceable via the phone number (you can then only be added with your "Username"). Once you've made your selections, click on "Next".

![Image](/assets/img/signal-guide-images/07.webp)

You're now connected to Signal and ready to exchange messages.

![Image](/assets/img/signal-guide-images/08.webp)

## Setting up your Signal account

Click on your profile photo in the top left-hand corner to access the application settings.

![Image](/assets/img/signal-guide-images/09.webp)

The "Account" menu lets you manage your profile settings. I advise you to keep the default settings. You can also activate the "Registration Lock" option, which protects your account against certain types of attack. This menu also contains the options you need to transfer your account to a new device.

![Image](/assets/img/signal-guide-images/10.webp)

Clicking again on your profile picture in the settings will take you to the options for personalizing your profile. I recommend that you set a "Username": this will enable you to get in touch with other people without exposing your phone number.

![Image](/assets/img/signal-guide-images/11.webp)

By selecting "QR code or link", you'll get the information you need to share with someone who wants to add you to Signal.

![Image](/assets/img/signal-guide-images/12.webp)

The "Privacy" menu is particularly important. Here you'll find options for controlling the visibility of your number, the management of your messages with your contacts, as well as various authorizations granted on the application.

![Image](/assets/img/signal-guide-images/13.webp)

And feel free to explore the "Appearance", "Chats" and "Notifications" menus to tailor Interface and the application's operation to your personal needs.

## Connect desktop application

To connect the desktop application, start by installing the software on your computer (see the first part of this tutorial). Then, on your phone, go to Settings and open the "Linked devices" section.

![Image](/assets/img/signal-guide-images/14.webp)

Click on the "Link a new device" button.

![Image](/assets/img/signal-guide-images/15.webp)

On your computer, launch the software, then scan the QR code displayed on the screen using your phone. If you wish to import your conversations, select the "Transfer message history" option.

![Image](/assets/img/signal-guide-images/16.webp)

Your devices are now fully synchronized.

![Image](/assets/img/signal-guide-images/17.webp)

## Sending messages with Signal

To communicate with someone on Signal, you first need to add them as a contact. There are several options: you can add them via their phone number (if the person has activated the option to be found by this means), or use their Signal ID.

Click on the pencil icon in the bottom right-hand corner of the Interface.

![Image](/assets/img/signal-guide-images/18.webp)

In my case, I want to add the person by username. So I click on "Find by username".

![Image](/assets/img/signal-guide-images/19.webp)

You can then either paste in its login or scan its QR code.

![Image](/assets/img/signal-guide-images/20.webp)

Send him a message to establish contact.

![Image](/assets/img/signal-guide-images/21.webp)

The conversation will then appear on the home page. If the person accepts your contact request, you'll see their name and profile photo.

![Image](/assets/img/signal-guide-images/22.webp)

Congratulations, you're now up to speed on using Signal messaging!