---
title: How to run a Monero Node
date: 2025-10-16 12:30:00 +0000
categories: [Guide, Security]
tags: [security,nodes,monero,server]     # TAG names should always be lowercase
description: Learn to run a Monero node via Systemd
# comments: false
author: monerocommunity
---

Running your own Monero node as a systemd service and adding Tor/I2P hidden services gives you a reliable, always‑on backend for your wallets and the network — with private, end‑to‑end encrypted access for desktop and mobile. This comprehensive guide covers downloading and verifying Monero binaries, installing and running monerod via systemd, exposing clearnet P2P/RPC, adding Tor/I2P, testing from server and client, wallet configuration, and debugging.

## End goal

You will publicly offer:

- Clearnet P2P (for other nodes): xxx.yyy.zzz.vvv:18080
- Clearnet RPC (for wallets): xxx.yyy.zzz.vvv:18089

> Public RPC (“public-node=1”) helps users without their own node, but can be resource‑intensive — monitor CPU, RAM, disk IO, and bandwidth.
{: .prompt-warning }

## Why this setup

- Always‑on sync via systemd (no intermittent manual runs)
- Optional public RPC broadcast so others can connect wallets through your node
- Private wallet access over Tor/I2P hidden services, bypassing NAT and mitigating MITM risks

> Hidden services are end‑to‑end encrypted and private by default. Using “--tx-proxy” also lets other nodes relay transactions to you over Tor/I2P.
{: .prompt-info }

## Assumptions

You have:

- Basic Linux admin skills
- Root access to a Linux server
- Recommended 4 GB+ RAM
- Recommended SSD storage sized for full or pruned blockchain (plan ~2.5× headroom over current sizes)
- Ubuntu examples; adapt to your distribution as needed

> Check the current blockchain sizes and plan extra space to accommodate growth and maintenance overhead.
{: .prompt-info }

## Download Monero

A single archive contains all you need to start using Monero (the full node and the wallet).

We recommend downloading Monero binaries directly from GitHub:

- GUI + CLI: [https://github.com/monero-project/monero-gui/releases](https://github.com/monero-project/monero-gui/releases)
- CLI only: [https://github.com/monero-project/monero/releases](https://github.com/monero-project/monero/releases)

GUI is a graphical desktop wallet. CLI is a command‑line desktop wallet.

If you need more guidance, see [download Monero](https://getmonero.org/downloads) on the official website.

> It is critical to verify the signature of the downloaded archive before using it.
{: .prompt-warning }

### Which version to download

- Download the latest version matching your operating system and processor architecture.
- The CLI version is released earlier and is suitable for server deployments.
- The GUI version contains both CLI and GUI and is preferable for end‑users.
- All versions contain a full node and a wallet.

### Why prefer GitHub over getmonero.org

- Binaries appear earlier on GitHub.
- If you fail to properly verify the signature, GitHub is safer than trusting a separate website not to be compromised.
- Signature verification is always your primary line of defense — verify every release.

## Verify Monero binaries (PGP + hash)

Verification must be completed before extracting or running anything.

> Do not extract or run binaries until verification succeeds.
{: .prompt-warning }

### 1) Import and trust the maintainer’s PGP key (one‑time)

Monero core developers sign a list of hashes of released binaries. The releases are signed by “binaryFate”.

Import binaryFate’s public key from the Monero source repository:

```bash
curl https://raw.githubusercontent.com/monero-project/monero/master/utils/gpg_keys/binaryfate.asc | gpg --import
```
> Adds the signer’s public key to your GPG keyring.
{: .prompt-info }

Ensure the fingerprint matches exactly: 81AC 591F E9C4 B65C 5806 AFC3 F0AF 4D46 2A0B DF92

### 2) Verify the signature on the published hash list

The file “hashes.txt” lists binary filenames and their hashes. It’s published on [getmonero.org](https://www.getmonero.org/downloads/hashes.txt).

Fetch and verify the signature:

```bash
curl https://www.getmonero.org/downloads/hashes.txt | gpg --verify
```
> Confirms the hashes list is signed by “binaryFate <binaryfate@getmonero.org>”.
{: .prompt-info }

Expected output includes:

```
gpg: Good signature from "binaryFate <binaryfate@getmonero.org>"
```

> The publication channel does not matter if the signature verifies correctly.
{: .prompt-tip }

### 3) Verify the downloaded archive’s hash matches the signed list

Compute the file’s SHA‑256 hash and compare it against the verified “hashes.txt”.

Set the filename (replace with your actual file):

```bash
file_name=monero-gui-linux-x64-<version>.tar.bz2
```
> Replace <version> with the exact filename you downloaded.
{: .prompt-info }

Compute the SHA‑256:

```bash
file_hash=$(sha256sum "$file_name" | cut -c 1-64)
```
> Captures the 64‑character SHA‑256 sum for your file.
{: .prompt-info }

Download the reference hashes and verify signature (again for completeness):

```bash
curl https://www.getmonero.org/downloads/hashes.txt > /tmp/reference-hashes.txt
gpg --verify /tmp/reference-hashes.txt
```
> Saves the signed hash list locally and re‑verifies its signature.
{: .prompt-info }

Ensure your file’s hash appears in the list:

```bash
grep "$file_hash" /tmp/reference-hashes.txt
```
> grep must print a line; empty output means a mismatch — do not proceed.
{: .prompt-danger }

If grep prints the filename and hash, your download is authentic and intact.

## Install Monero (server)

### Create the service user

```bash
useradd --system monero
```
> Creates a dedicated system user to run monerod securely.
{: .prompt-tip }

### Create config, data, and log directories

```bash
mkdir -p /etc/monero     # config
mkdir -p /var/lib/monero # blockchain
mkdir -p /var/log/monero # logs
chown monero:monero /etc/monero
chown monero:monero /var/lib/monero
chown monero:monero /var/log/monero
```
> Prepares directories and ownership for monerod runtime files.
{: .prompt-tip }

Feel free to adjust conventions; ensure paths match in the monerod config and systemd unit.

### Extract the binaries

```bash
tar -xvf monero-linux-x64-<version>.tar.bz2
rm monero-linux-x64-<version>.tar.bz2
```
> Unpacks Monero CLI/daemon binaries and removes the archive.
{: .prompt-info }

### Move binaries into place

```bash
mv monero-x86_64-linux-gnu-<version>/* /usr/local/bin/.
chown monero:monero /usr/local/bin/monero*
```
> Installs binaries into PATH and sets ownership to the monero user.
{: .prompt-tip }

## Monerod config

Create “/etc/monero/monerod.conf” (example):

```ini
# /etc/monero/monerod.conf
#
# Configuration file for monerod. For all available options see the MoneroDocs:
# https://docs.getmonero.org/interacting/monerod-reference/

# Data directory (blockchain db and indices)
data-dir=/var/lib/monero/bitmonero   # Blockchain storage location

# Optional pruning
#prune-blockchain=1           # Pruning saves 2/3 of disk space w/o degrading functionality but contributes less to the network
#sync-pruned-blocks=1         # Allow downloading pruned blocks instead of prunning them yourself

# Centralized services
check-updates=disabled         # Do not check DNS TXT records for a new version
enable-dns-blocklist=1         # Block known malicious nodes

# Banlist
#ban-list=/path/to/ban.txt      # Local list of peers to ban

# Log file
log-file=/var/log/monero/monero.log
log-level=0                    # Minimal logs, WILL NOT log peers or wallets connecting
max-log-file-size=2147483648   # Set to 2GB to mitigate log trimming by monerod; configure logrotate instead

# P2P full node
#p2p-bind-ip=0.0.0.0            # Bind to all interfaces (the default)
#p2p-bind-port=18080            # Bind to default port
#no-igd=1                       # Disable UPnP port mapping

# RPC open node
#public-node=1                  # Advertise to other users they can use this node for connecting their wallets
rpc-restricted-bind-ip=0.0.0.0 # Bind to all interfaces (the Open Node)
rpc-restricted-bind-port=18089 # Bind to a new RESTRICTED port (the Open Node)

# RPC TLS
rpc-ssl=autodetect             # Use TLS if client wallet supports it; [enabled|disabled|(default)autodetect]

# ZMQ
#zmq-rpc-bind-ip=127.0.0.1      # Default 127.0.0.1
#zmq-rpc-bind-port=18082        # Default 18082
zmq-pub=tcp://127.0.0.1:18083  # ZMQ pub
#no-zmq=1                       # Disable ZMQ RPC server

# Mempool size
max-txpool-weight=2684354560   # Maximum unconfirmed transactions pool size in bytes (here ~2.5GB, default ~618MB)

# Database sync mode
#db-sync-mode=safe:sync        # Slow but reliable db writes

# Network limits
out-peers=12              # Default 12
in-peers=48               # The default is unlimited; we prefer to put a cap on this

limit-rate-up=1048576     # 1048576 kB/s == 1GB/s; a raise from default 2048 kB/s; contribute more to p2p network
limit-rate-down=1048576   # 1048576 kB/s == 1GB/s; a raise from default 8192 kB/s; allow for faster initial sync

# Tor/I2P: broadcast transactions originating from connected wallets over Tor/I2P (does not concern relayed transactions)
#tx-proxy=i2p,127.0.0.1:4447,12,disable_noise  # I2P
#tx-proxy=tor,127.0.0.1:9050,12,disable_noise  # Tor

# Tor/I2P: tell monerod your onion address so it can be advertised on P2P network
#anonymous-inbound=PASTE_YOUR_I2P_HOSTNAME,127.0.0.1:18085,24         # I2P
#anonymous-inbound=PASTE_YOUR_ONION_HOSTNAME:18084,127.0.0.1:18084,24 # Tor

# Tor: be forgiving to connecting wallets
disable-rpc-ban=1
```
> Ensure paths and ports match your directories and firewall rules
{: .prompt-info }

## Run monerod via systemd

### Create the systemd unit

```ini
# /etc/systemd/system/monerod.service

[Unit]
Description=Monero Daemon
After=network-online.target

[Service]
ExecStart=/usr/local/bin/monerod --detach --config-file /etc/monero/monerod.conf --pidfile /run/monero/monerod.pid
ExecStartPost=/bin/sleep 0.1
PIDFile=/run/monero/monerod.pid
Type=forking

Restart=on-failure
RestartSec=30

User=monero
Group=monero
RuntimeDirectory=monero

StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```
> Runs monerod as a forking service, restarts on failure, and logs to journal.
{: .prompt-info }

### Enable and start the service

```bash
systemctl daemon-reload
systemctl enable monerod
systemctl restart monerod
```
> Reloads unit files, enables autostart, and starts the service now.
{: .prompt-tip }

### Verify status and logs

```bash
systemctl status monerod
```
> Confirms service status, PID, and recent journal entries.
{: .prompt-info }

```bash
tail -n100 /var/log/monero/monero.log
```
> Reviews last 100 lines for sync progress and warnings/errors.
{: .prompt-info }

## Open firewall ports (clearnet)

Open TCP ports for incoming connections if you use a firewall (recommended):

```bash
ufw allow 18080/tcp
ufw allow 18089/tcp
```
> Allows P2P (18080) and RPC (18089) over IPv4/IPv6. No ports needed for Tor/I2P.
{: .prompt-tip }

Verify with “ufw status”; you should see the allowed rules.

## Tor and I2P

### Why use anonymity networks

You can connect your desktop and mobile Monero wallets to your own trusted Monero node, in a secure and private way over Tor or I2P.

- Hidden service connections are end‑to‑end encrypted and private by default.
- Hidden services bypass NAT and mitigate real MITM risks.
- Onion and I2P for P2P allow other nodes to relay transactions to your node (using “--tx-proxy”).

> Tor/I2P reduce metadata exposure and remove reliance on public RPC endpoints you don’t control.
{: .prompt-info }

### Node configuration (Tor/I2P)

- Tor: enable the local SOCKS proxy (default 127.0.0.1:9050), and set “tx-proxy=tor,socks,127.0.0.1:9050” in monerod.conf as needed; optionally expose onion P2P/RPC via hidden services.
- I2P: enable the local SOCKS proxy (e.g., 127.0.0.1:4447), and set “tx-proxy=i2p,socks,127.0.0.1:4447” in monerod.conf; optionally expose I2P services for P2P/RPC.

> You do not need to open onion/I2P ports externally; hidden services operate through local proxies and outbound connections.
{: .prompt-info }

> (Optional) Publish your node on [monero.fail](https://monero.fail) to help users find reliable nodes.
{: .prompt-tip }

## Testing

### On the server

```bash
sudo netstat -lntpu
```
> Confirms listening services: P2P (18080), RPC (18089), onion P2P (18084 local if configured), Tor SOCKS (9050).
{: .prompt-info }

Expected lines (PIDs will differ):
- 0.0.0.0:18080 LISTEN monerod
- 0.0.0.0:18089 LISTEN monerod
- 127.0.0.1:18084 LISTEN monerod (onion P2P if configured)
- 127.0.0.1:9050 LISTEN tor

### On the client machine

Install “tor” and “torsocks”; “proxychains” helps test P2P.

Test clearnet P2P:

```bash
nc -zv YOUR_IP_ADDRESS_HERE 18080
```
> Checks TCP reachability of your P2P port.
{: .prompt-info }

Test clearnet RPC:

```bash
curl --digest -X POST http://YOUR_IP_ADDRESS_HERE:18089/json_rpc \
-d '{"jsonrpc":"2.0","id":"0","method":"get_info"}' \
-H 'Content-Type: application/json'
```
> Confirms RPC access and node info response over clearnet.
{: .prompt-info }

Test onion P2P (requires proxychains):

```bash
proxychains nc -zv YOUR_IP_ADDRESS_HERE 18084
```
> Verifies onion P2P listening via local proxy chain (if configured).
{: .prompt-info }

Test onion RPC:

```bash
curl -x socks5h://127.0.0.1:9050 --digest -X POST \
http://YOUR_ONION_ADDRESS_HERE.onion:18089/json_rpc \
-d '{"jsonrpc":"2.0","id":"0","method":"get_info"}' \
-H 'Content-Type: application/json'
```
> Confirms wallet RPC over Tor to your onion service.
{: .prompt-info }

## Wallet setup (Tor/I2P)

To connect Monero wallets, configure the SOCKS proxy and remote node.

### Tor

#### Monero GUI

1. Settings → Interface → Socks5 proxy: IP Address 127.0.0.1, Port 9050
2. Settings → Node → Add remote node: Address http://yourlongv3onionaddress.onion, Port 18089

#### Monero CLI

```bash
--proxy=127.0.0.1:9050 \
--daemon-address=http://yourlongv3onionaddress.onion:18089 \
--trusted-daemon
```
> Configures CLI to use Tor SOCKS and your onion RPC endpoint.
{: .prompt-tip }

### I2P

#### Monero GUI

1. Settings → Interface → Socks5 proxy: IP Address 127.0.0.1, Port 4447
2. Settings → Node → Add remote node: Address http://yourlongb32i2paddress.b32.i2p, Port 18089

#### Monero CLI

```bash
--proxy=127.0.0.1:4447 \
--daemon-address=http://yourlongb32i2paddress.b32.i2p:18089 \
--trusted-daemon
```
> Configures CLI to use I2P SOCKS and your I2P RPC endpoint.
{: .prompt-tip }

## Debugging

### Tor

```bash
systemctl status tor@default
```
> Shows service state and recent events.
{: .prompt-info }

```bash
journalctl -xe --unit tor@default
```
> Prints detailed Tor logs for troubleshooting.
{: .prompt-info }

### Monerod

```bash
systemctl status monero
```
> Confirms monerod service state and last logs.
{: .prompt-info }

```bash
tail -n100 /var/log/monero/monero.log
```
> Checks recent daemon output for warnings/errors.
{: .prompt-info }

Increase log detail (revert after solving):

- Set “log-level=1” (from “log-level=0”) in your monerod.conf

> Raising log level helps isolate issues; remember to reset to reduce noise.
{: .prompt-warning }

## Conclusion

Running Monero as a systemd service with Tor/I2P hidden services gives you a robust, privacy‑preserving node that stays in sync, serves the network, and offers secure wallet connectivity anywhere. Download from trusted sources, verify every release, configure carefully, and test both server and client paths. With public RPC as an option and hidden services for private access, your node can support both the broader ecosystem and your own sovereign use — reliably and securely.