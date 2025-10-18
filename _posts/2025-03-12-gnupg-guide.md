---
title: GnuPG Guide (GPG)
date: 2025-03-12 12:30:00 +0000
categories: [Guide, Security]
tags: [security,authentication,gpg,gnupg,sparrow,authenticity]     # TAG names should always be lowercase
description: How to verify the integrity and authenticity of software?
# comments: false
authors: [loicpandul, vibrant]
image: /assets/img/gpg-guide-images/cover.webp
---

When downloading software, it's very important to ensure that it hasn't been altered and that it indeed comes from the official source. This is especially true for software related to Bitcoin, such as wallet software, which allows you to secure the keys giving access to your funds. In this tutorial, we will see how to verify the integrity and authenticity of software before installing it. We will use Sparrow Wallet as an example, a favorite wallet software among bitcoiners, but the procedure will be the same for any other software.

Verifying integrity involves ensuring that the downloaded file has not been modified by comparing its digital fingerprint (i.e., its hash) with the one provided by the official developer. If the two match, it means that the file is identical to the original and has not been corrupted or modified by an attacker.

Verifying authenticity, on the other hand, ensures that the file indeed comes from the official developer and not an impostor. This is done by verifying a digital signature. This signature proves that the software was signed with the legitimate developer's private key.

> If you skip these checks, you risk installing malware that can steal private keys or corrupt files — do not install until both authenticity and integrity are verified.
{: .prompt-danger }

To perform this verification, we will use two tools: hashing functions to verify integrity, and GnuPG, an open-source tool that implements the PGP protocol, to verify authenticity.

## Prerequisites

If you are on Linux, GPG is preinstalled on most distributions. If not, you can install it with the following command:

```bash
sudo apt install gnupg
```

For macOS, if you haven't already installed the Homebrew package manager, do so with the following commands:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
```

```bash
eval "$(/opt/homebrew/bin/brew shellenv)"
```

Then install GPG with this command:

```bash
brew install gnupg
```

For Windows, if you do not have GPG, you can install the [Gpg4win](https://www.gpg4win.org/) software.

![Image](/assets/img/gpg-guide-images/01.webp)

## Downloading Documents

To start, we will need various documents. Visit the official site of [Sparrow Wallet in the "Download" section](https://sparrowwallet.com/download/). If you wish to verify another software, go to that software's website.

![Image](/assets/img/gpg-guide-images/02.webp)

You can also go [to the GitHub repository of the project](https://github.com/sparrowwallet/sparrow/releases).

![Image](/assets/img/gpg-guide-images/03.webp)

Download the installer for the software corresponding to your operating system.

![Image](/assets/img/gpg-guide-images/04.webp)

You will also need the file's hash, often called "SHA256SUMS" or "MANIFEST".

![Image](/assets/img/gpg-guide-images/05.webp)

Download the PGP signature of the file as well. This is the document in .asc format.

![Image](/assets/img/gpg-guide-images/06.webp)

> Download installers, hashes, and signatures only from official release pages to reduce spoofed or tampered assets.
{: .prompt-warning }

> Place the installer, hash/manifest, and .asc signature in the same folder to simplify verification commands.
{: .prompt-tip }

Finally, you will need the developer's public key, which we will use to verify the PGP signature. This key is often available either on the software's website, on the GitHub repository of the project, sometimes on the developer's social media, or on specialized sites like Keybase. In the case of Sparrow Wallet, you can find developer Craig Raw's public key [on Keybase](https://keybase.io/craigraw). To download it directly from the terminal, execute the command:

```bash
curl https://keybase.io/craigraw/pgp_keys.asc | gpg --import
```

![Image](/assets/img/gpg-guide-images/07.webp)

> Verify the public key fingerprint from multiple independent sources (site, GitHub, Keybase, social) before trusting it; do not rely on a single source.
{: .prompt-warning }

## Verifying the Signature

The process of verifying the signature is the same on Windows, macOS, and Linux. Normally, you have already imported the public key during the previous step, but if not, do so with the command:

```bash
gpg --import [key path]
```

Replace "[key path]" with the location of the developer's public key file.

![Image](/assets/img/gpg-guide-images/08.webp)

Verify the signature with the following command:

```bash
gpg --verify [file.asc]
```

Replace "[file.asc]" with the path of the signature file. In the case of Sparrow, this file is called "sparrow-2.0.0-manifest.txt.asc" for version 2.0.0.

![Image](/assets/img/gpg-guide-images/09.webp)

> If signature verification fails or references an unexpected key, do not proceed — the asset may be tampered or signed by an impostor.
{: .prompt-danger }

If the signature is valid, GPG will indicate this to you. You can then move on to the next step, as this confirms the file's authenticity.

![Image](/assets/img/gpg-guide-images/10.webp)

> A “good signature” is only meaningful if the signing key’s fingerprint matches the developer’s independently published fingerprint.
{: .prompt-tip }

## Verifying the Hash

Now that the software's authenticity has been confirmed, it's also necessary to verify its integrity. We will compare the software's hash with the hash provided by the developer. If the two match, it guarantees that the software code has not been altered.

On Windows, open a terminal and execute the following command:

```bash
CertUtil -hashfile [file path] SHA256 | findstr /v "hash"
```

Replace "[file path]" with the location of the installer.

![Image](/assets/img/gpg-guide-images/11.webp)

The terminal will return the hash of the downloaded software.

![Image](/assets/img/gpg-guide-images/12.webp)

> Some projects use different algorithms (e.g., SHA512). Use the algorithm specified by the developer for accurate comparison.
{: .prompt-info }

Then compare the result with the corresponding value in the file "sparrow-2.0.0-manifest.txt".

![Image](/assets/img/gpg-guide-images/13.webp)

In my case, we see that the two hashes match perfectly.

On macOS and Linux, the hash verification process is automated. It's not necessary to manually check the match between the two hashes as on Windows.

Simply execute this command on macOS:

```bash
shasum --check [file name] --ignore-missing
```

Replace "[file name]" with the name of the installer. For example, for Sparrow Wallet:

```bash
shasum --check sparrow-2.0.0-manifest.txt --ignore-missing
```

If the hashes match, you should see the following output:

```bash
Sparrow-2.0.0.dmg: OK
```

On Linux, the command is similar:

```bash
sha256sum --check [file name] --ignore-missing
```

And if the hashes match, you should see the following output:

```bash
sparrow_2.0.0-1_amd64.deb: OK
```

> If the hash does not match, do not install — re-download from the official source and repeat verification.
{: .prompt-danger }

You are now assured that the software you've downloaded is both authentic and intact. You can proceed with its installation on your machine.

> Repeat these checks for every update or re-install; never trust cached files without re-verification.
{: .prompt-tip}