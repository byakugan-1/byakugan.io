---
title: How to Ban Bitcoin Knots Nodes
date: 2025-10-12 12:30:00 +0000
categories: [Guide, Security]
tags: [security,nodes,bitcoin core,bitcoin knots,server]     # TAG names should always be lowercase
description: Learn to Ban Knots Nodes from Peering with your Core node
# comments: false
---

Credit: [@noosphere888x2](https://x.com/noosphere888x2) for creating this tool.

Your node is your lens into Bitcoin. If you prefer Bitcoin Core’s standard relay behavior and don’t want to peer with clients that enforce different defaults, the Bitcoin Knots Node Ban Script gives you a straightforward, automated way to detect and disconnect (or ban) Knots peers — and keep your mempool view aligned with Core.

## Why you might want this

Bitcoin Knots implements restrictive transaction relay policies. In practice, that can interfere with normal network operations and quietly block privacy‑preserving workflows many users rely on.

- Core‑aligned relay: Keep your node’s mempool and peer connections in line with Core’s standard policies
- Fewer policy surprises: Reduce mismatches that can affect fee estimation and transaction timing
- Privacy motivation: Knots’ defaults don’t relay common privacy workflows (e.g., PayNym/BIP47 notifications via OP_RETURN, Whirlpool Tx0 coordination), so pruning Knots peers can help reduce breakage for privacy tooling in your network path

> Client choice affects your security, privacy, and reliability. It’s not a cosmetic preference.
{: .prompt-warning }

> Local policy only affects your node’s relay and peer view. It does not change consensus rules or stop miners from including transactions.
{: .prompt-info }

## What you’ll set up

A plug‑and‑play script that:
- Installs with one command and runs on a schedule (cron)
- Works with any Bitcoin Core node that has RPC enabled
- Detects Knots peers by subversion string and optional IP banlist
- Can disconnect or ban, with configurable duration and a dry‑run mode
- Saves discovered IPs locally for future runs
- Handles IPv4, IPv6, and Tor addresses safely
- Logs actions with basic statistics and color‑coded output

Originally based on [Samourai Dojo’s ban-knots.sh](https://github.com/Dojo-Open-Source-Project/samourai-dojo/blob/develop/docker/my-dojo/bitcoin/ban-knots.sh).

## Before you begin

You’ll need:
- A running Bitcoin Core node with RPC enabled
- jq (JSON processor) and bash
- Shell access to the machine (SSH is fine)

Typical targets:
- Bitcoin Core on Linux/macOS
- Umbrel, RaspiBlitz, MyNode, RaspiBolt
- Start9 (with persistence caveats; see notes below)

## Install in minutes

Pick the path that fits your setup. Each step includes a short note so you know what’s happening.

### Option A — Automatic detection (fastest)

Use this for Bitcoin Core, Umbrel, RaspiBlitz, MyNode, RaspiBolt. The script auto‑finds your cookie or bitcoin.conf and installs cron.

```bash
wget https://github.com/noosphere888/Ban-Knots/releases/download/v1.2.2/standalone-ban-knots.sh
```
> Downloads the standalone script.

```bash
chmod +x standalone-ban-knots.sh
```
> Makes the script executable.

```bash
./standalone-ban-knots.sh --install-cron
```
> Installs a cron job to run every 10 minutes with auto‑detected auth.

> Check “crontab -l” to confirm the recurring job is set. Logs go to a temp file you can tail.
{: .prompt-tip }

Optionally enable the external IP banlist (from a community repo) and merge it with your local discoveries:

```bash
./standalone-ban-knots.sh --external-ban-list --install-cron
```
> Adds the external list and installs cron.

> Enabling the external banlist contacts GitHub. Use Tor (e.g., torsocks) or a VPN if you care about metadata leakage.
{: .prompt-warning }

### Option B — Manual RPC credentials

Use this if auto‑detection fails or you’re on Start9 (explicit creds typical).

```bash
wget https://github.com/noosphere888/Ban-Knots/releases/download/v1.2.2/standalone-ban-knots.sh
chmod +x standalone-ban-knots.sh
```
> Downloads and prepares the script.

```bash
./standalone-ban-knots.sh -u yourrpcuser -P yourrpcpass --install-cron
```
> Installs cron using your RPC username/password.

### Option C — Config file (rarely needed)

Pin host/port or store credentials in a simple config the script can read.

```bash
cat > ~/.bitcoin/ban-knots.conf << 'EOF'
rpc_host=127.0.0.1
rpc_port=8332
rpc_user=yourusername
rpc_password=yourpassword
ban_duration=157680000
disconnect_only=false
EOF
```
> Creates a config file with your settings.

```bash
./standalone-ban-knots.sh -c ~/.bitcoin/ban-knots.conf --install-cron
```
> Installs cron using the config file.

## Verify and monitor

- Show installed cron jobs
  ```bash
  crontab -l
  ```
  > Confirms the job is scheduled.

- Follow the log
  ```bash
  tail -f /tmp/ban-knots.log
  ```
  > See detections and actions in real time.

> Safe preview: “./standalone-ban-knots.sh --dry-run” shows what would happen without changing anything.
{: .prompt-tip }

## Everyday usage and tuning

- Disconnect only (no ban)
  ```bash
  ./standalone-ban-knots.sh --disconnect-only
  ```
  > Light‑touch pruning that avoids long bans.

- Adjust ban duration (example: 30 days in seconds)
  ```bash
  ./standalone-ban-knots.sh -d 2592000
  ```
  > Sets a shorter ban if you prefer.

- Enable external banlist later
  ```bash
  ./standalone-ban-knots.sh --external-ban-list
  ```
  > Merges upstream IPs with your local discoveries.

- Use a custom cookie path
  ```bash
  ./standalone-ban-knots.sh --cookie-path /custom/path/.cookie
  ```
  > Helpful for nonstandard installs.

> Review logs periodically; if pruning is too aggressive, shorten the ban or switch to disconnect‑only.
{: .prompt-info }

## Platform notes

### Start9
- Read‑only filesystem for custom scripts; cron jobs may not persist across updates/restarts
- Script detects and uses podman to talk to Bitcoin Core in-container
- For permanence, run the script from a separate machine that can reach your Start9 RPC over Tor

> After Start9 updates, reinstall the script/cron if you ran them on the device. For long‑term stability, manage it from an external host.
{: .prompt-warning }

### Umbrel
- Auto‑detection typically “just works”
- Use “--umbrel” only if detection fails or you’re running the script from outside the Umbrel host

## How it works (under the hood)

- Enumerates peers via RPC and reads subversion strings
- Detects “Knots/bitcoinknots” (primary signal)
- Optionally merges a community IP banlist with your local discoveries
- Extracts base IP (supports IPv4/IPv6/Tor safely)
- Disconnects and/or bans the IP and logs the action
- Saves discovered IPs for future detection

> External banlist is disabled by default for privacy. If enabled, prefer Tor/VPN during downloads.
{: .prompt-warning }

## Troubleshooting

- Cannot connect to node
  - Ensure Bitcoin Core is running and RPC is enabled
  - Verify cookie path or RPC creds; check firewall
  - Confirm host/port; install jq via your package manager

- Bans appear to do nothing
  - The peer may already be banned
  - Check Bitcoin Core logs for RPC permission issues

- Start9 quirks
  - Run on the Start9 host for auto‑detection or supply creds explicitly
  - For persistence, run from another host over Tor and point at the Start9 RPC

> Tail “/tmp/ban-knots.log” to see detections and actions live. Use “--dry-run” to test safely.
{: .prompt-info }

## Closing thoughts

Sovereignty isn’t about choosing the strictest knob; it’s about the path that best preserves your security, privacy, and agency. If your goal is Core‑aligned relay and fewer surprises in mempool behavior — especially to avoid breaking widely used privacy tools — automating Knots peer pruning can be a practical, low‑maintenance step. Tune gently, watch your logs, and keep enough good peers. Your node, your policy.