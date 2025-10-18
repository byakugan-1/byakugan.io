---
title: How to Run a Bitcoin Node
date: 2025-09-22 12:30:00 +0000
categories: [Guide, Privacy]
tags: [bitcoin, node, fulcrum, dojo, robosats, monero]     # TAG names should always be lowercase
description: Learn to Build a verifiable, Tor‑ready Bitcoin Node stack
# comments: false
pin: true
image: /assets/img/bitcoin-node-guide-images/header.png
---

### You don’t outsource freedom. You build it.

This guide is your path to running Bitcoin like a sovereign operator: auditable, verifiable, private by default. No app store gatekeepers. No waiting for Start9 or Umbrel to ship an update. You decide what runs, and why - because you understand it.

## What you’ll build

- Hardware: Mini PC (Intel N100, 16 GB RAM, 2 TB NVMe)
- OS: Ubuntu Server 24.04 LTS (headless)
- Network posture: Mullvad VPN for fast initial sync; then Tor‑only Bitcoin Core

Services:
- Bitcoin Core (verified, supervised with systemd)
- Fulcrum (Electrum server) over LAN and a Tor hidden service
- mempool.space via Docker (LAN + Tor)
- Samourai Dojo via Docker
- Robosats via Docker
- Monero node via Docker (simple‑monerod credit: [SethForPrivacy](https://sethforprivacy.com/guides/run-a-monero-node/)) with Tor hidden service

- Ops: PGP verification, Docker primer, firewall, SSH hardening, backups, upgrades, and troubleshooting

## Constraints and mindset

- This is a guide. You are responsible for your own due diligence, local laws, and ongoing maintenance. Basically DYOR
- Every command includes inline comments to explain what it does.
- When facts are uncertain (e.g., ecosystem changes), we state so and offer trade‑offs.
- Expect to learn Linux basics. It’s an investment that pays dividends across your digital life.

## 1) Why this manual path beats plug‑and‑play
Running a node is about enforcing rules you accept. Doing it manually ensures you actually know which rules you’re enforcing and how.

- Trust minimization: Node distros (like Start9 and Umbrel) add layers - opinionated packaging, dashboards, custom scripts. More code surface, more trust in their release and update process.
- Update sovereignty: You choose when to update. No waiting for a platform to push an app version. You can verify, stage, roll back.
- Auditability: You know exactly what runs. Your configs are readable. Your services are plain systemd units or Docker Compose files you control.
- Efficiency: Only install what you need. Less bloat, fewer daemons, smaller attack surface.
- Skills dividend: Basic Linux and Docker competency is invaluable. It makes you harder to censor and easier to self‑support.

## 2) What a node does (and why it matters)
A Bitcoin node is your fake‑Bitcoin detector. It downloads and verifies every block and transaction from genesis under the rules you choose (like the 21 million cap), and lets you broadcast your transactions without permission.

- Verification: Balances and incoming payments are only “real” when your node says they are.
- Censorship resistance: Broadcast on your schedule, not a custodian’s.
- Privacy: Query your own infrastructure; stop creating logs on third‑party servers.

## 3) Hardware profile and storage planning
Baseline (minimum target):

- CPU: Intel N100 (efficient, modern)
- RAM: 16 GB DDR4/DDR5 (helps Fulcrum fast‑sync and future headroom)
- Storage: 2 TB NVMe SSD (txindex=1 + Fulcrum DB + OS + growth)
- Network: Ethernet (wired beats Wi‑Fi for reliability)
- Power: Consider a small UPS or use a device with a battery if you can (Optional but recommended)

Storage outlook (approximate; grows over time):

- Bitcoin Core (full, txindex=1): ~700–800 GB today, growing
- Fulcrum DB: ~170 GB initial, grows with chain
- Monero full node: ~250 GB today, growing
- OS + logs + Docker layers: ~30–60 GB

Pruning trade‑off: You could prune Core to reduce disk, but then you lose features that depend on historical data. This guide uses txindex=1.

Network planning:

- Set a static DHCP lease for your node in your router to avoid IP changes.
- Enable AC recovery “Power On” in BIOS so your node auto‑boots after power loss.

## 4) BIOS/UEFI and Ubuntu Server install
Goal: A minimal, long‑lived base OS that survives outages and doesn’t get in your way.

BIOS/UEFI:
- Boot order: USB first for install; then NVMe.
- UEFI: Keep enabled. Disable Secure Boot if it blocks Ubuntu Server.
- AC recovery: “Power On” so the machine starts automatically after outages.

Install Ubuntu 24.04 LTS Server:
- [Guide from Ministry of Nodes](https://www.youtube.com/watch?v=d7ulwBUL18A)
- Minimal install (no GUI).
- Enable OpenSSH Server during install.
- On first boot, unplug install media; keep only Ethernet + power.

After install:
- Note your node’s IP from your router or via display during install.
- From your laptop/desktop, you will SSH into the node for everything else.

## 5) Linux command lexicon for beginners
You only need a short toolkit to run a secure node. You’ll see these throughout the guide.

### Navigation and files
```bash
pwd
```
> Print working directory (where am I?)

```bash
ls -la
```
> List files (including hidden), long format

```bash
cd /path
```
> Change directory to /path

```bash
mkdir /path/dir
```
> Create a new directory

```bash
rm -rf /path/dir
```
> Remove directory and contents (dangerous; triple-check path)

### Editing text
```bash
nano /path/file
```
> Open file in nano editor; Ctrl+O to write, Ctrl+X to exit

### Packages and updates
```bash
sudo apt update
```
> Refresh package indexes (what updates are available)

```bash
sudo apt upgrade -y
```
> Install available updates (security + bug fixes)

### Disk and logs
```bash
df -h
```
> Show disk usage by filesystem (human-readable)

```bash
du -sh /path
```
> Show total size of a directory

```bash
tail -f /var/log/syslog
```
> Follow a log file in real time

### Services (systemd)
```bash
sudo systemctl enable name.service
```
> Enable on boot (create symlink)

```bash
sudo systemctl start name.service
```
> Start service now

```bash
sudo systemctl status name.service
```
> Check service state/log tail

```bash
journalctl -u name.service -f
```
> Follow service logs in real time

- Also ctrl+k can be used to delete entire lines when editing files with nano

### SSH and copy/paste
- Use your terminal application (macOS/Linux) or PowerShell (Windows).
- In many terminals, paste is Ctrl+Shift+V, not Ctrl+V.
- Tab autocompletion is your friend; use it to avoid typos in long paths.
- You can use the up/down arrowkeys to go to previous/next commands you have entered in your terminal's bash history
- Entering ''sudo !!" runs your last command with sudo

### Backups (what you might consider backing up after this guide)
Configs and secrets, not blockchains:
- ~/.bitcoin/bitcoin.conf
- Fulcrum: ~/fulcrum/fulcrum.conf, ~/fulcrum/cert.pem, ~/fulcrum/key.pem
- Docker Compose files and .env files
- Dojo secrets and admin keys
- Tor hidden service directories (each service’s hostname/private_key)
- rpcauth credentials or rpcuser/rpcpassword if used

## 6) Hardening and quality of life
Security and operability steps worth doing early.

### Enable unattended security updates
```bash
sudo apt install -y unattended-upgrades
```
> Install auto security patcher

```bash
sudo dpkg-reconfigure -plow unattended-upgrades
```
> Enable automatic security updates

### Optional: mDNS for easy hostname (nodebox.local)
```bash
sudo apt install -y avahi-daemon
```
> Enables .local hostname discovery on LAN

```bash
# After install, you can reach the node as: ssh youruser@nodebox.local
# (or whatever yout hostname is instead of nodebox)
```

### Optional: Cockpit (browser UI at [nodebox.local:9090](nodebox.local:9090))
```bash
sudo apt install -y cockpit
```
> Simple web UI for health, logs, and a shell

### SSH key authentication and disable password logins (recommended but totally optional)
```bash
mkdir -p ~/.ssh && chmod 700 ~/.ssh
```
> Create SSH dir with secure perms

```bash
nano ~/.ssh/authorized_keys
```
> Paste your public key; save file

```bash
chmod 600 ~/.ssh/authorized_keys
```
> Secure the authorized keys file

```bash
sudo nano /etc/ssh/sshd_config
```
> Set: PasswordAuthentication no; PubkeyAuthentication yes

```bash
sudo systemctl reload ssh
```
> Apply SSH daemon changes without disconnect

### Firewall (UFW) basics
Note: UFW can break things if you do not use it properly so ensure you understand how to use this first, watch this guide:

{% include embed/youtube.html id='-CzvPjZ9hp8' %}

```bash
sudo apt install -y ufw
```
> Install uncomplicated firewall

```bash
sudo ufw default deny incoming
```
> Block inbound connections by default

```bash
sudo ufw default allow outgoing
```
> Allow outbound by default

```bash
sudo ufw allow OpenSSH
```
> Allow SSH from your LAN

```bash
sudo ufw enable
```
> Turn on firewall (be sure SSH works first)

## 7) Mullvad first, then Tor: your staged privacy model
We’ll use Mullvad VPN to hide IBD from your ISP and improve throughput, then switch Bitcoin Core to Tor‑only for steady‑state privacy. This will also be useful for maintaining some privacy from your ISP when running your monero node.

### Install Mullvad CLI (from Mullvad’s docs; adjust if they change URLs)
```bash
# Download the Mullvad signing key
sudo curl -fsSLo /usr/share/keyrings/mullvad-keyring.asc https://repository.mullvad.net/deb/mullvad-keyring.asc

# Add the Mullvad repository server to apt
echo "deb [signed-by=/usr/share/keyrings/mullvad-keyring.asc arch=$( dpkg --print-architecture )] https://repository.mullvad.net/deb/stable stable main" | sudo tee /etc/apt/sources.list.d/mullvad.list

# Install the package
sudo apt update
sudo apt install mullvad-vpn
```

### Initial Mullvad setup
```bash
mullvad account login <YOUR_MULLVAD_ACCOUNT_NUMBER>  # Activates your device
mullvad lan set allow                                # Prevents locking yourself out of the node
mullvad relay set location                           # Example: pick a location (e.g., Sweden Gothenburg)
mullvad auto-connect set on                          # Connect VPN automatically on boot
mullvad connect                                       # Bring up the VPN now
mullvad status                                        # Verify connection and selected exit
mullvad lockdown-mode set on                          # (optional) Disables all traffic that is not through the VPN
```

Notes:
- “lan set allow” is critical; without it, local connections to your node may fail when VPN is active.
- You can choose a relay closer to you for better performance. [List here](https://mullvad.net/servers).

## 8) Verify, install, and supervise Bitcoin Core
Treat Bitcoin Core like critical infrastructure: verify, configure, supervise.

### Create a working directory
```bash
mkdir -p ~/downloads
cd ~/downloads
```
> Keep downloads organized

### Download Bitcoin Core (adjust version as needed)
```bash
# Replace version with the latest stable from bitcoincore.org
BITCOIN_VERSION=29.1
wget https://bitcoincore.org/bin/bitcoin-core-${BITCOIN_VERSION}/bitcoin-${BITCOIN_VERSION}-x86_64-linux-gnu.tar.gz
wget https://bitcoincore.org/bin/bitcoin-core-${BITCOIN_VERSION}/SHA256SUMS
wget https://bitcoincore.org/bin/bitcoin-core-${BITCOIN_VERSION}/SHA256SUMS.asc
```

### Verify checksums
```bash
sha256sum --ignore-missing -c SHA256SUMS
```
> Should show “OK” for the tarball you downloaded

### Import a trusted PGP key (e.g., fanquake/Michael Ford) and verify signature
Bitcoin releases are signed by a number of individuals, each with a unique public key. In order to recognize the validity of signatures, you must use GPG to load these public keys locally. You can find many developer keys listed in the [bitcoin-core/guix.sigs repository](https://github.com/bitcoin-core/guix.sigs/tree/main/builder-keys), which you can then load into your GPG key database.

For example, you could load the key builder-keys/fanquake.gpg by downloading the file as fanquake.gpg and using this command:

```bash
wget https://raw.githubusercontent.com/bitcoin-core/guix.sigs/refs/heads/main/builder-keys/fanquake.gpg
gpg --import fanquake.gpg
```

The output of the command above should say that one key was imported, updated, has new signatures, or remained unchanged.

It is recommended that you choose a few individuals from this list who you find trustworthy and import their keys as above. You will later use their keys to check the signature attesting to the validity of the checksums you use to check the binaries. You can import all keys at once by cloning the repo and importing the directory:

```bash
git clone https://github.com/bitcoin-core/guix.sigs
gpg --import guix.sigs/builder-keys/*
```

Verify that the checksums file is PGP signed by a sufficient amount of keys you trust and have imported into your keychain:

```bash
gpg --verify SHA256SUMS.asc
```

The command above will output a series of signature checks for each of the public keys that signed the checksums. Each valid signature will show the following text:The output from the verify command may contain warnings that a public key is not available. As long as you have all the public keys of signers you trust, this warning can be disregarded. There may be additional warnings that a "key is not certified with a trusted signature." This means that to fully verify your download, you need to confirm that the signing key's fingerprint (e.g. E777 299F.. .) listed in the second line above matches what you had expected for the signers public key. See the GNU handbook section on [key management](https://www.gnupg.org/gph/en/manual/x334.html) for more details.

A line that starts with: gpg: Good signature

A complete line saying: Primary key fingerprint: E777 299F C265 DD04 7930  70EB 944D 35F9 AC3D B76A

### Install binaries to /usr/local/bin
```bash
tar xzf bitcoin-${BITCOIN_VERSION}-x86_64-linux-gnu.tar.gz   # Extract the tarball
sudo install -m 0755 -o root -g root -t /usr/local/bin bitcoin-${BITCOIN_VERSION}/bin/*  # Copy binaries system-wide
```

### Create bitcoin.conf
```bash
mkdir -p ~/.bitcoin
nano ~/.bitcoin/bitcoin.conf
```

Recommended initial config (IBD over VPN, not Tor yet):
```ini
# ~/.bitcoin/bitcoin.conf
server=1
daemon=1
txindex=1

# RPC (local only)
rpcbind=127.0.0.1
rpcallowip=127.0.0.1
rpcport=8332

# Credentials (use rpcauth for stronger security if exposing RPC beyond localhost)
# Replace with values you generate below (rpcauth), or set rpcuser/rpcpassword for local tools like Fulcrum.
rpcauth=USERNAME:HASHOFPASSWORD

# ZMQ for real-time block/tx notifications (used by Fulcrum/mempool)
zmqpubrawblock=tcp://127.0.0.1:28332
zmqpubrawtx=tcp://127.0.0.1:28333
zmqpubhashblock=tcp://127.0.0.1:28334

# Networking (IBD stage: use default clearnet over VPN)
listen=1

whitelist=127.0.0.1
rpcallowip=127.0.0.1
rpcallowip=10.0.0.0/8
rpcallowip=172.0.0.0/8
rpcallowip=192.0.0.0/8
```

Generate rpcauth and add the USERNAME:HASHOFPASSWORD to the above file:
```bash
# Download helper script from Bitcoin Core repo to generate rpcauth line
cd ~/downloads
wget https://raw.githubusercontent.com/bitcoin/bitcoin/master/share/rpcauth/rpcauth.py
chmod +x rpcauth.py
./rpcauth.py YourUsername YourStrongPassword  # Outputs an rpcauth line to put in bitcoin.conf
```

Copy the string to be appended to bitcoin.conf

### Start bitcoind once to initialize data dir
```bash
bitcoind -daemon
``>
> Starts Bitcoin Core in the background using your config

### Follow logs and check sync state
```bash
tail -f ~/.bitcoin/debug.log
```
> Watch sync progress in real time (Ctrl+C to stop)

```bash
bitcoin-cli getblockchaininfo
```
> Snapshot of headers/blocks sync status

```bash
bitcoin-cli getconnectioncount
```
> How many peers you’re connected to

### Create a systemd service so Core auto‑starts on boot
```bash
sudo nano /etc/systemd/system/bitcoind.service
```

Paste:
```
# It is not recommended to modify this file in-place, because it will
# be overwritten during package upgrades. If you want to add further
# options or overwrite existing ones then use
# $ systemctl edit bitcoind.service
# See "man systemd.service" for details.

# Note that almost all daemon options could be specified in
# /etc/bitcoin/bitcoin.conf, but keep in mind those explicitly
# specified as arguments in ExecStart= will override those in the
# config file.

[Unit]
Description=Bitcoin daemon
Documentation=https://github.com/bitcoin/bitcoin/blob/master/doc/init.md

# https://www.freedesktop.org/wiki/Software/systemd/NetworkTarget/
After=network-online.target
Wants=network-online.target

[Service]
ExecStart=/usr/local/bin/bitcoind -daemon \
                            -pid=/run/bitcoind/bitcoind.pid \
                            -conf=/home/satoshi/.bitcoin/bitcoin.conf \
                            -datadir=/home/satoshi/.bitcoin

# Make sure the config directory is readable by the service user
PermissionsStartOnly=true
#ExecStartPre=/bin/chgrp bitcoin /etc/bitcoin

# Process management
####################

Type=forking
PIDFile=/run/bitcoind/bitcoind.pid
Restart=on-failure
TimeoutStartSec=infinity
TimeoutStopSec=600

# Directory creation and permissions
####################################

# Run as bitcoin:bitcoin
User=satoshi
Group=satoshi

# /run/bitcoind
RuntimeDirectory=bitcoind
RuntimeDirectoryMode=0710

# /etc/bitcoin
ConfigurationDirectory=bitcoin
ConfigurationDirectoryMode=0710

# /var/lib/bitcoind
StateDirectory=bitcoind
StateDirectoryMode=0710

# Hardening measures
####################

# Provide a private /tmp and /var/tmp.
PrivateTmp=true

# Mount /usr, /boot/ and /etc read-only for the process.
ProtectSystem=full

# Deny access to /home, /root and /run/user
#ProtectHome=true

# Disallow the process and all of its children to gain
# new privileges through execve().
NoNewPrivileges=true

# Use a new /dev namespace only populated with API pseudo devices
# such as /dev/null, /dev/zero and /dev/random.
PrivateDevices=true

# Deny the creation of writable and executable memory mappings.
MemoryDenyWriteExecute=true

[Install]
WantedBy=multi-user.target
```

### Enable and start (replace “satoshi” with your username if different)
```bash
sudo systemctl enable bitcoind@satoshi.service
```
> Enable templated service for your user

```bash
sudo systemctl start bitcoind@satoshi.service
```
> Start Core under your user account

```bash
systemctl status bitcoind@satoshi.service
```
> Confirm running

IBD will take a few hours to a few day depending on your hardware and internet. That’s fine - Mullvad conceals it from your ISP and often improves throughput. When done:

bitcoin-cli getblockchaininfo shows headers == blocks and "initialblockdownload": false

## 9) Flip to Tor‑only Core and add hidden services
### Install Tor and set up control access (so apps can use Tor programmatically)
```bash
sudo apt install -y tor
sudo nano /etc/tor/torrc
```

Edit torrc (uncomment/add):
```
ControlPort 9051
CookieAuthentication 1
CookieAuthFileGroupReadable 1
```

### Apply and verify Tor
```bash
sudo systemctl reload tor
```
> Reload Tor config

```bash
sudo ss -tulpn | grep tor | grep LISTEN
```
> Confirm Tor is listening on 127.0.0.1:9050 and 9051

```bash
sudo journalctl -f -u tor@default
```
> View Tor logs (Ctrl+C to exit)

### Reconfigure Bitcoin Core to use Tor‑only for P2P
```bash
nano ~/.bitcoin/bitcoin.conf
```

Delete old contents of bitcoin.conf and replace with this below:
```ini
# ------------------------------
# Networking & Tor Configuration
# ------------------------------

# Accept only Tor (.onion) peers — used after initial block download (IBD)
onlynet=onion

# Route all traffic through Tor's SOCKS5 proxy
proxy=127.0.0.1:9050

# Bind to localhost (for security, no external IPs)
bind=127.0.0.1

# ------------------------------
# Node Operation Settings
# ------------------------------

# Enable JSON-RPC server (for remote control, wallet, etc.)
server=1

# Enable transaction index (stores all transactions for querying via RPC)
txindex=1

# Run in the background as a daemon (Unix only)
daemon=1


# ------------------------------
# RPC (Remote Procedure Call) Settings
# ------------------------------

# Set RPC server port (default is 8332)
rpcport=8332

# Listen for RPC connections on all interfaces
rpcbind=0.0.0.0

# Allow RPC connections from localhost
rpcallowip=127.0.0.1

# Allow RPC from common private network ranges (customize as needed)
rpcallowip=10.0.0.0/8
rpcallowip=172.0.0.0/8
rpcallowip=192.0.0.0/8

# ⚠️ Note: This opens RPC server to LAN. Make sure authentication is enabled!


# ------------------------------
# ZMQ (ZeroMQ) Event Broadcasting
# ------------------------------

# Publish raw block data to this TCP address
zmqpubrawblock=tcp://0.0.0.0:28332

# Publish raw transaction data
zmqpubrawtx=tcp://0.0.0.0:28333

# Publish block hash notifications
zmqpubhashblock=tcp://0.0.0.0:28334

# ⚠️ ZMQ binds to all interfaces (0.0.0.0)


# ------------------------------
# Whitelisting
# ------------------------------

# Never ban localhost even if misbehaving
whitelist=127.0.0.1


# ------------------------------
# RPC Authentication (REQUIRED if rpcbind is not localhost)
# ------------------------------

rpcauth=username:hash-of-password


# Allow creation of legacy wallets (required for JoinMarket if you decide to use that, you should be whirlpooling instead!)
deprecatedrpc=create_bdb

maxmempool=2000

# Filters - for Bisq
peerbloomfilters=1
```

### Restart Core and confirm Tor reachability
```bash
sudo systemctl restart bitcoind@satoshi.service
bitcoin-cli getnetworkinfo
```
> onion reachable: true; ipv4/ipv6 reachable: false

```bash
bitcoin-cli getpeerinfo
```
> Peer addresses show .onion

```bash
bitcoin-cli getnetworkinfo | grep -A3 localaddresses
```
> This is your bitcoin p2p address

### Create Tor hidden services for remote access to specific apps
We will expose Fulcrum (50002), mempool.space (web UI on 4080), and Monero restricted RPC (18089).
```bash
sudo nano /etc/tor/torrc
```

Append per‑service hidden services:
```
############### This section is just for location-hidden services ###

# Hidden Service: Fulcrum (SSL 50002)
HiddenServiceDir /var/lib/tor/hs_fulcrum_ssl/
HiddenServiceVersion 3
HiddenServicePort 50002 127.0.0.1:50002

# Hidden Service: Fulcrum (TCP)
HiddenServiceDir /var/lib/tor/hs_fulcrum_tcp/
HiddenServiceVersion 3
HiddenServicePort 50001 127.0.0.1:50001

# Hidden Service: mempool.space
HiddenServiceDir /var/lib/tor/hs_mempool/
HiddenServiceVersion 3
HiddenServicePort 80 127.0.0.1:4080

# Hidden Service: Robosats
HiddenServiceDir /var/lib/tor/hs_robosats/
HiddenServiceVersion 3
HiddenServicePort 80 127.0.0.1:12596

# Hidden Service: Monero restricted RPC (18089)
HiddenServiceDir /var/lib/tor/hs_monero_rpc/
HiddenServiceVersion 3
HiddenServicePort 18089 127.0.0.1:18089
```

### Reload and retrieve onion addresses
```bash
sudo find /var/lib/tor/ -type f -name hostname -path "/var/lib/tor/hs_*/hostname" -exec sh -c 'echo "{}"; cat "{}"; echo' \;
```

Important: Back up each hidden service directory (it contains the private key that controls the onion identity).

## 10) Fulcrum: your private Electrum server
Why Fulcrum? It indexes the chain for fast wallet queries and keeps your wallet’s addresses on your node, not on random public servers. Faster and more private.

### Create directories and download a release
```bash
mkdir -p ~/fulcrum ~/fulcrum_db
cd ~/downloads
# Replace version as needed (see Fulcrum releases)
wget https://github.com/cculianu/Fulcrum/releases/download/v2.0.0/Fulcrum-2.0.0-x86_64-linux.tar.gz
tar xzf Fulcrum-1.12.0-x86_64-linux.tar.gz
mv Fulcrum-2.0.0-x86_64-linux/* ~/fulcrum/    # Put binaries in ~/fulcrum
```

### Generate LAN keys for encrypted Electrum connections
```bash
cd ~/fulcrum
openssl req -newkey rsa:2048 -new -nodes -x509 -days 3650 -keyout key.pem -out cert.pem
```
> Creates a self-signed cert pair; leave prompts blank or fill generically

### Create and edit config
```bash
cp fulcrum-example-config.conf fulcrum.conf  # Name may vary; use the example shipped with your release
nano fulcrum.conf
```

Key settings to adapt:
```ini
datadir = /home/satoshi/fulcrum_db

# Bitcoin Core RPC (local)
bitcoind = 127.0.0.1:8332
rpcuser = bitcoin
rpcpassword = bitcoin

# Serve SSL to clients on LAN (and to Tor hidden service)
tcp = 0.0.0.0:50001
ssl = 0.0.0.0:50002
cert = /home/satoshi/fulcrum/cert.pem
key = /home/satoshi/fulcrum/key.pem

#banner is optional and you may remove
banner = /home/satoshi/fulcrum/banner.txt

# Performance and behavior
peering = false
db_mem = 5120
```

### Create optional banner
in ~/fulcrum nano a banner.txt and add a fun ASCII art to make your node your own. You will see this when you connect to sparrow. It can be a lighthearted way to verify you are connected to your own server. [Here](https://patorjk.com/software/taag/) is a useful ASCII art creation website.

### Create a systemd service for auto startup at boot
```bash
sudo nano /etc/systemd/system/fulcrum.service
```

Paste:
```
[Unit]
Description=Fulcrum
After=network.target bitcoind@satoshi.service
Requires=bitcoind@satoshi.service

[Service]
ExecStart=/home/satoshi/fulcrum/Fulcrum /home/satoshi/fulcrum/fulcrum.conf
User=satoshi
LimitNOFILE=8192
TimeoutStopSec=30min
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

### Enable, start, and monitor
```bash
sudo systemctl enable fulcrum.service
sudo systemctl start fulcrum.service
journalctl -u fulcrum.service -f
```
> Watch the index build; expect many hours and ~170+ GB

### Sparrow wallet connections
- LAN: Add your node’s LAN IP (or nodebox.local) and port 50002 in Sparrow, check “Use SSL.” [On first connect some wallets will allow you to view and pin your self‑signed cert/fingerprint].
- Tor: In Sparrow, enable tor, and use your Fulcrum onion with port 50002 (SSL). Pin the cert on first connect.

## 11) mempool.space via Docker (LAN + Tor)
Why Docker here? mempool.space is a multi‑service app (frontend, API, backend indexers). Docker Compose gives a clean one‑file definition, isolated dependencies, and reproducible updates.

### Install Docker and join the docker group
```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

### To install the latest version, run
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### Clone mempool repo and prepare Compose
```bash
cd ~
git clone https://github.com/mempool/mempool.git
cd mempool/docker
```

### Edit docker-compose.yml (key changes)
- Map frontend port to 4080 on the host.
- Set backend to “electrum” and point to Fulcrum.
- Provide Core RPC credentials and ZMQ endpoints.

Example docker-compose.yml snippet (adapt to upstream format if they change service names):
```yaml
version: "3.7"

services:
  web:
    environment:
      FRONTEND_HTTP_PORT: "8080"
      BACKEND_MAINNET_HTTP_HOST: "api"
    container_name: mempool-web
    image: mempool/frontend:v3.2.1
    user: "1000:1000"
    restart: always
    stop_grace_period: 1m
    command: "./wait-for db:3306 --timeout=720 -- nginx -g 'daemon off;'"
    ports:
      - 4080:8080
  api:
    environment:
      MEMPOOL_BACKEND: "none"
      MEMPOOL_BACKEND: "electrum"
      ELECTRUM_HOST: "172.17.0.1"
      ELECTRUM_PORT: "50002"
      CORE_RPC_HOST: "172.17.0.1"
      CORE_RPC_PORT: "8332"
      CORE_RPC_USERNAME: "bitcoin"
      CORE_RPC_PASSWORD: "bitcoin"
      DATABASE_ENABLED: "true"
      DATABASE_HOST: "db"
      DATABASE_DATABASE: "mempool"
      DATABASE_USERNAME: "mempool"
      DATABASE_PASSWORD: "mempool"
      STATISTICS_ENABLED: "true"
    image: mempool/backend:v3.2.1
    container_name: mempool-api
    user: "1000:1000"
    restart: always
    stop_grace_period: 1m
    command: "./wait-for-it.sh db:3306 --timeout=720 --strict -- ./start.sh"
    volumes:
      - ./data:/backend/cache
  db:
    environment:
      MYSQL_DATABASE: "mempool"
      MYSQL_USER: "mempool"
      MYSQL_PASSWORD: "mempool"
      MYSQL_ROOT_PASSWORD: "admin"
    container_name: mempool-db
    image: mariadb:10.5.21
    user: "1000:1000"
    restart: always
    stop_grace_period: 1m
    volumes:
      - ./mysql/data:/var/lib/mysql

networks:
  default:
    driver: bridge
    ipam:
      config:
        - subnet: 172.16.57.0/24
```

To avoid network conflicts this has been added to the bottom of the file:
```bash
networks:
  default:
    driver: bridge
    ipam:
      config:
        - subnet: 172.16.57.0/24
```

### Start it up
```bash
docker compose up -d
```
> Launches the stack in the background

```bash
docker compose ps
```
> Shows running containers and ports

### Access mempool.space
- LAN: [nodebox.local:4080](nodebox.local:4080) (or your node IP)
- Tor: Use the mempool onion created earlier; visit with Tor Browser

### To Update
```bash
docker compose pull
docker compose up -d
```
> Pull new images and recreate containers with new images

## 12) Samourai Dojo via Docker
Dojo gives Samurai‑ecosystem wallets a private backend you control (like Ashigaru). The Ashigaru fork emerged after the indictment of the samourai wallet devs - [read](https://www.therage.co/samourai-wallet-developers-plead-guilty-to-unlicensed-money-transmission/). The wallet server functionality still matters for private use.

Full Credit to K3tan of Ministry of nodes - [Support him here please](https://www.ministryofnodes.com.au/support)

Follow this video guide to setup Samourai Dojo step by step:

{% include embed/youtube.html id='8_It4xEIwRA' %}

### Dojo Maintenance Tool via Tor
- The installer typically prints an onion URL for the admin interface.
- Visit in Tor Browser, enter your admin key, and wait for all subsystems to go green.
- Pair your Samourai/Ashigaru wallet using the QR in the “Pairing” tab.

## 13) Robosats via Docker
Robosats is a Tor‑native P2P exchange. I will show you how to self host it for the most sovereign experience when buying bitcoin peer-to-peer.

### Step 1: Clone the RoboSats Repository
Clone the official RoboSats GitHub repo.
```bash
git clone https://github.com/RoboSats/robosats.git
```

### Step 2: Navigate to the Project Directory
Change into the cloned directory and then into the nodeapp subdirectory.
```bash
cd robosats/
cd nodeapp/
```

### Step 3: Prepare the Docker Compose File
Rename the example Docker Compose file to the actual file Docker uses.
```bash
mv docker-compose-example.yml docker-compose.yml
```

### Step 4: Edit Docker Compose Configuration
If needed, open the file for editing:
```bash
nano docker-compose.yml
```

Change
```bash
container_name: tor
```

To
```bash
container_name: robosats-tor
```

### Step 5: Start the RoboSats Docker Services
Build and start the Docker containers in detached mode:
```bash
docker compose up -d
```

You might see a warning about the version field — it's safe to ignore or remove from docker-compose.yml if desired.

Use this to access your own robosats instance over tor or just use your server's ip with port 12596

## 14) Monero node via Docker (simple‑monerod) + Tor
Monero complements Bitcoin with generally more private by default transactions. Running your own node prevents wallet leaks to remote RPC servers.

### Recommended run (restricted RPC + watchtower)
```bash
# Monero daemon (restricted RPC exposed on 18089; P2P on 18080)
docker run -d --restart unless-stopped --name="monerod" \
  -p 18080:18080 -p 18089:18089 \
  -v bitmonero:/home/monero \
  ghcr.io/sethforprivacy/simple-monerod:latest \
  --rpc-restricted-bind-ip=0.0.0.0 \
  --rpc-restricted-bind-port=18089 \
  --no-igd --no-zmq --enable-dns-blocklist

# Watchtower auto-updates (monerod and tor if present)
docker run -d --name watchtower --restart unless-stopped \
  -v /var/run/docker.sock:/var/run/docker.sock \
  containrrr/watchtower --cleanup \
  monerod tor
```

### Logs and status
```bash
docker logs --follow monerod
```
> Follow daemon logs

```bash
docker exec monerod /usr/local/bin/monerod status
```
> Short status: peers, height, sync

```bash
docker exec monerod /usr/local/bin/monerod sync_info
```
> Detailed sync info

```bash
docker exec monerod /usr/local/bin/monerod print_net_stats
```
> Traffic stats and limits

```bash
docker exec monerod /usr/local/bin/monerod update check
```
> Check for new version

### Expose Monero restricted RPC over Tor
```bash
# We already added a Tor hidden service for Monero RPC in torrc earlier (hs_monero_rpc).
sudo cat /var/lib/tor/hs_monero_rpc/hostname
```
> Use in your wallet settings

## 15) Maintenance, upgrades, and backups
Sovereignty is a practice, not a product. Put maintenance on your calendar.

### OS and packages
```bash
sudo apt update && sudo apt upgrade -y
```
> Periodic manual updates alongside unattended upgrades

### Bitcoin Core upgrades
```bash
# Stop Core, verify new release, install, then start
sudo systemctl stop bitcoind@satoshi.service
# Repeat the download + checksum + PGP verify steps from earlier with the new version
tar xzf bitcoin-<NEW>.tar.gz
sudo install -m 0755 -o root -g root -t /usr/local/bin bitcoin-<NEW>/bin/*
sudo systemctl start bitcoind@satoshi.service
bitcoin-cli -version
```

### Fulcrum upgrades
```bash
sudo systemctl stop fulcrum.service
# Replace ~/fulcrum binaries with new release after verifying checksums/signature if provided
sudo systemctl start fulcrum.service
journalctl -u fulcrum.service -f
```

### mempool.space upgrades
```bash
cd ~/mempool/docker
docker compose pull
docker compose up -d
```

### Monero upgrades
Watchtower restarts your container on new tags automatically, no manual input neccesary.

### Backups (configs and keys, not block data)
- ~/.bitcoin/bitcoin.conf and rpcauth/rpc credentials
- ~/fulcrum/fulcrum.conf, cert.pem, key.pem
- Docker Compose files and any .env files
- Dojo secrets/admin key (and any wallet pairing info)
- Tor hidden service directories:
  - /var/lib/tor/hs_fulcrum
  - /var/lib/tor/hs_mempool
  - /var/lib/tor/hs_monero_rpc
  - (and any others you create like hs_robosats)

- Store backups encrypted, off the machine. Test restores.

### Power and shutdown
- Always shut down gracefully to protect Fulcrum and databases.
- Consider a small UPS; configure the BIOS AC “Power On” setting.

## 16) Threat models and pitfalls
### Who sees what?
- IBD over Mullvad (clearnet through VPN): ISP sees VPN traffic; Mullvad sees volume/destinations, not Bitcoin payloads; Bitcoin peers see your VPN egress IP.
- Tor‑only Core: ISP sees Tor usage (unless over VPN); peers see only your onion identity; you avoid clearnet IP exposure.
- LAN services: Anyone on your LAN can see open ports; protect with firewall and strong auth.

### Common foot‑guns
- Mullvad without “lan allow” can lock you out; set it before connecting.
- Wrong ExecStart path (/usr vs /usr/local/bin) breaks systemd units..
- Fulcrum DB corruption from hard power loss; always shut down cleanly - This should not be an issue after the main release of Fulcrum 2.0.
- Disk exhaustion; keep headroom and monitor df -h.
- PGP trust: Verify fingerprints out‑of‑band where possible; don’t blindly trust keyservers.

### When asking for help
- Share logs and configs selectively (redact secrets and onion hostnames).
- Use paste services with expiry; never post private keys or admin tokens.

## 17) Appendix: operator’s cheat sheet
### Frequently used commands
```bash
# System
df -h
du -sh /path
journalctl -u name.service -f
sudo systemctl {start,stop,restart,status,enable,disable} name.service

# Bitcoin
tail -f ~/.bitcoin/debug.log
bitcoin-cli getblockchaininfo
bitcoin-cli getnetworkinfo
bitcoin-cli getpeerinfo
bitcoin-cli gettxoutsetinfo

# Docker
docker compose ps
docker compose logs -f
docker ps
docker logs -f <container>

# Tor
sudo systemctl reload tor
sudo cat /var/lib/tor/<hs_name>/hostname
```

### Key file paths
- Bitcoin: ~/.bitcoin/bitcoin.conf
- Fulcrum: ~/fulcrum/fulcrum.conf, ~/fulcrum/cert.pem, ~/fulcrum/key.pem
- systemd units: /etc/systemd/system/*.service
- mempool: ~/mempool/docker/docker-compose.yml
- Dojo: ~/dojo-app/docker/my-dojo/*.tpl and generated configs
- Tor hidden services: /var/lib/tor/*/ (hostname, private_key)

### Ports overview
Service
Local port(s)
Tor HS port mapping
Notes

Bitcoin Core RPC
8332
n/a
RPC local only (default)

Bitcoin P2P
8333 (loopback)
8333 -> 127.0.0.1:8333
Bitcoin p2p Tor

Bitcoin Core ZMQ
28332/28333/28334
n/a
For block/tx notifications

Fulcrum SSL
50002
50002 -> 127.0.0.1:50002
Electrum Server

mempool.space
4080 (HTTP)
80 -> 127.0.0.1:4080
Use Tor Browser

Monero RPC (restricted)
18089
18089 -> 127.0.0.1:18089
Wallet RPC

Tor control
9051
n/a
CookieAuth enabled

Tor SOCKS
9050
n/a
Local proxy

## Closing: The necessary journey
Sovereignty isn’t a product you buy. It’s a practice you adopt. You’ve now built a Bitcoin stack you can explain, verify, and repair. You know where the binaries came from, which keys signed them, what every service does, and how to turn any of it off. You can patch when you want, roll back when you need, and expose only what you intend—over Tor, on your terms.

## Next steps
- Connect Sparrow to Fulcrum (LAN or Tor) and verify you can view and broadcast transactions.
- Use your local mempool.space (ideally over tor) to inspect fees and monitor transactions without leaking queries.
- Pair Ashigaru to your Dojo if you use that wallet.
- Keep good backups of configs and Tor hiddenservice addresses, and schedule maintenance.

You didn’t just install software - you claimed responsibility. That’s what sovereign stacking looks like.

Credits to [https://raspibolt.org/](https://raspibolt.org/) + [https://sethforprivacy.com/](https://sethforprivacy.com/) + [https://k3tan.com/](https://k3tan.com/) for providing us with the knowledge required. Show them support.

For any corrections, edits or suggestions please contact me on signal at vibrant.01 or on X at [https://x.com/VibrantBTC](https://x.com/VibrantBTC)

Share this if it was helpful and support the people/projects credited above.
