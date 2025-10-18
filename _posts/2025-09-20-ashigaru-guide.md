---
title: Ashigaru Terminal Guide
date: 2025-09-20 12:30:00 +0000
categories: [Guide, Privacy]
tags: [ashigaru, ashigaru terminal, privacy]     # TAG names should always be lowercase
description: Learn how to setup and use Ashigaru Terminal for use in your browser
# comments: false
pin: true
author: zeroxsix
image: /assets/img/ashigaru-terminal-guide-images/header.png
---

This guide walks you through setting up the Ashigaru Terminal on a secure Ubuntu/Debian system using:

- A dedicated system user (ashigaru)
- Persistent tmux session
- Web access via ttyd
- Boot-time auto-launch using systemd

## Initial Step: Install Tor
Before beginning, install Tor so you can safely access the Ashigaru files over the Tor network.

```bash
sudo apt update
```
> Updates the list of available packages to ensure you're installing the latest versions

```bash
sudo apt install -y tor
```
> Installs the Tor package to access the internet securely and anonymously through the Tor network

```bash
sudo systemctl enable --now tor
```
> Starts Tor and enables it to run at boot so you can always have a secure connection

## Part 1: Create an Isolated User (Optional)
```bash
sudo adduser ashigaru
```
> Starts Tor and enables it to run at boot so you can always have a secure connection

```bash
sudo usermod -aG sudo ashigaru
```
> Adds the new user to the sudo group, granting administrative privileges so they can execute commands as root when necessary

```bash
sudo -i -u ashigaru
```
> Switches to the new user (ashigaru) to ensure all actions are performed under this user’s context for isolation and security

## Part 2: Install Ashigaru Terminal
### Download Files Using Tor:
```bash
torsocks wget http://ashicodepbnpvslzsl2bz7l2pwrjvajgumgac423pp3y2deprbnzz7id.onion/Ashigaru/Ashigaru-Terminal/releases/download/v1.0.0/ashigaru_terminal_v1.0.0_amd64.deb
```
> Downloads the Ashigaru Terminal .deb file securely through the Tor network using torsocks to route traffic via Tor.

```bash
torsocks wget http://ashicodepbnpvslzsl2bz7l2pwrjvajgumgac423pp3y2deprbnzz7id.onion/Ashigaru/Ashigaru-Terminal/releases/download/v1.0.0/ashigaru_terminal_v1.0.0_signed_hashes.txt
```
> Downloads the signed hash file to verify the integrity and authenticity of the downloaded Ashigaru package.

For those who wish to verify the authenticity of these URLs, you can visit the official Ashigaru Tor repository linked at the bottom of their official website: [ashigaru.rs](https://ashigaru.rs)

### Import the PGP Key:
```bash
curl https://keybase.io/ashigarudev/pgp_keys.asc | gpg --import
```
> Imports the PGP key to verify the authenticity of the Ashigaru files using cryptographic signatures

### Verify their Signature:
```bash
gpg --verify ashigaru_terminal_v1.0.0_signed_hashes.txt
```
> Verifies the signature of the downloaded hash file to ensure it hasn’t been tampered with

### Check the Hash:
```bash
sha256sum ashigaru_terminal_v1.0.0_amd64.deb
```
> Calculates the hash of the downloaded package to compare it with the verified hash in the signed file

```bash
cat ashigaru_terminal_v1.0.0_signed_hashes.txt
```
> Displays the signed hash file, so you can manually check the hashes for correctness

### Install Ashigaru:
```bash
sudo dpkg -i ashigaru_terminal_v1.0.0_amd64.deb
```
> Installs the Ashigaru Terminal package after confirming that the file is authentic and secure

## Part 3: Persistent tmux Session at Boot
### Install tmux:
```bash
sudo apt update
```
> Updates available packages to ensure all dependencies are up to date

```bash
sudo apt install -y tmux
```
> Installs tmux, a terminal multiplexer that allows you to keep terminal sessions running persistently

### Create systemd Service:
```bash
sudo nano /etc/systemd/system/ashigaru.service
```
> Creates a systemd service to automatically start the Ashigaru terminal in tmux at boot

```
[Unit]
Description=Start Ashigaru terminal in tmux at boot
After=network.target

[Service]
Type=oneshot
RemainAfterExit=yes
User=ashigaru
WorkingDirectory=/home/ashigaru
ExecStartPre=/bin/sleep 60
ExecStart=/usr/bin/tmux new-session -d -s ashigaru '/opt/ashigaru-terminal/bin/Ashigaru-terminal'
ExecStop=/usr/bin/tmux kill-session -t ashigaru

[Install]
WantedBy=multi-user.target
```
> Paste this into the systemd service file

### Enable and Start:
```bash
sudo systemctl daemon-reload
```
> Reloads systemd's configuration to recognize the new service file you just created

```bash
sudo systemctl enable ashigaru.service
```
> Enables the service to start on boot, ensuring Ashigaru terminal is always running in tmux

```bash
sudo systemctl start ashigaru.service
```
> Starts the Ashigaru terminal service immediately, so it's up and running without rebooting

## Part 4: Web browser Access via ttyd
### Install ttyd:
```bash
sudo apt install -y ttyd
```
> Installs ttyd, a tool that allows you to expose terminal sessions (like tmux) in a web browser

### Create systemd Service for ttyd:
```bash
sudo nano /etc/systemd/system/ttyd.service
```
> Creates a systemd service for ttyd to automatically expose the Ashigaru terminal session in a web browser at boot

```
[Unit]
Description=ttyd exposing Ashigaru tmux session
After=network.target

[Service]
User=ashigaru
Group=ashigaru
ExecStart=/usr/bin/ttyd -W -p 7682 tmux attach-session -t ashigaru
Restart=always
RestartSec=5
Environment=TERM=xterm-256color

[Install]
WantedBy=multi-user.target
```
> Paste this into the systemd service file

### Enable and Start:
```bash
sudo systemctl daemon-reload
```
> Reloads systemd to recognize the new ttyd service configuration

```bash
sudo systemctl enable ttyd.service
```
> Enables the ttyd service to start on boot, making the Ashigaru terminal accessible via a web browser at boot

```bash
sudo systemctl start ttyd.service
```
> Starts the ttyd service immediately, allowing you to access the Ashigaru terminal via the web interface in any local web browser using: http://<your-server-ip>:7682

```bash
sudo reboot
```
> Reboot your system to ensure everything is applied correctly

Always do your own research and make decisions based on your own informed judgment

Edited by: [+headymouth97](https://paynym.rs/+headymouth97)
