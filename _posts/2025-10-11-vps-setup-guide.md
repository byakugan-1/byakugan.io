---
title: Linux VPS Setup and Hardening Guide
date: 2025-10-08 12:30:00 +0000
categories: [Guide, Privacy]
tags: [vps,security,mynymbox,hosting,server]     # TAG names should always be lowercase
description: Learn to securely set up and harden a Debian or Ubuntu VPS step by step
# comments: false
pin: true
image: /assets/img/linux-vps-guide-images/header.png
---

A VPS is one of those things that looks simple right up until the moment you deploy one and realise the wider internet has already noticed.

The basic workflow is easy enough: rent a server, get an IP address, log in over SSH, install what you wanted to run, and carry on with your day.

The real workflow is slightly different.

You rent the server, get the IP address, and before you have even finished admiring your fresh machine, the password guessing starts. That is not paranoia. That is just how public IPv4 works. Automated scanners sweep the entire IPv4 address space continuously. Within minutes of a new VPS coming online, bots will find it and begin probing for weak passwords, open ports, and known vulnerabilities. A new VPS is not a quiet cottage in the countryside. It is an unlocked shed dropped into the middle of a rough industrial estate.

**Securing your VPS is not optional. It is not something you get around to later. It is the very first thing you do.** If you skip this step, you are not taking a small risk. You are handing your server to the first script or botnet that finds it. Every minute an unsecured VPS sits on the public internet is a minute it is being actively probed, tested, and potentially compromised.

So the first rule is non-negotiable: do not install your application first. Secure the box first. No exceptions.

This guide walks through a clean Ubuntu or Debian based VPS setup in the order that actually makes sense:

- buy the VPS privately if you can;
- log in carefully;
- update the system;
- create a non-root user;
- harden SSH;
- lock down the firewall;
- enable automatic security updates;
- set sane system defaults;
- add a private mesh network;
- remove public SSH entirely if you want the stronger setup.

The goal here is not to produce some theatrical “military grade” server fantasy. The goal is much more boring and much more useful: reduce obvious attack surface, avoid stupid mistakes, and end up with a box that is calm, understandable, and reasonably difficult to mess with.

## Buy a VPS privately

If privacy matters to you, the process starts before you ever type your first SSH command.

Choose providers that minimise data collection and support privacy-preserving payments:


- [mynymbox.io](https://mynymbox.io)
- [1984.hosting](https://1984.hosting)
- [Hostinger](https://hostinger.com)

If possible, pay with Bitcoin after a Whirlpool coinjoin, Bitcoin via Lightning, or Monero. The point is not cosplay. The point is reducing unnecessary linkage between your real-world identity and a machine that may eventually host personal services, websites, automation, or tools you would prefer not to tie directly back to your legal name.

When the provider allows it, avoid giving more information than necessary. Use a fresh email address. Keep billing metadata minimal. Privacy is not one big magic trick. It is a chain of smaller decisions that stop you from handing over more context than the situation actually requires.

## Pick a sensible VPS size

For a basic personal server, you usually do not need much.

A good starting point is:

- 2 vCPUs
- 4 GB RAM
- 50 to 100 GB storage
- a suitable amount of bandwidth for your use case (most providers include 1–3 TB monthly, which is more than enough for personal infrastructure)

That is plenty for many self-hosted tools, websites, dashboards, VPN utilities, lightweight bots, and general personal infrastructure. Two cores give you enough headroom to run a few services concurrently without things grinding to a halt, and 4 GB of RAM keeps you comfortable for most lightweight self-hosting workloads.

If all you are doing is hardening a small box for later use, do not overthink it. You can always resize later. It is usually better to start small with a clean and understandable machine than to immediately rent a larger one you do not yet know how to manage.

## Choose Ubuntu or Debian

This guide assumes a Debian-based system.

Ubuntu LTS and Debian Stable are both perfectly reasonable choices:

- **Ubuntu LTS**
  - wider tutorial coverage
  - very beginner friendly
  - broad package support
  - a bit more bloated by default
- **Debian Stable**
  - lighter baseline
  - extremely dependable
  - excellent for long-running servers
  - packages can be older

If you are newer to Linux servers, Ubuntu LTS is fine.
If you prefer a slightly leaner default base and do not mind a little less hand-holding, Debian is excellent.

## Step 1: prepare an SSH key locally

Before touching the server, make sure you have a proper SSH key on your local machine.

List existing keys:

```bash
ls -la ~/.ssh/
```

> Shows the SSH material already on your computer. This helps you see whether you already have keys you can reuse, and whether your `~/.ssh` directory looks sane.

If you do not already have a dedicated key for this VPS, create one:

```bash
ssh-keygen -t ed25519 -C "vps-admin-key"
```

> Generates a modern Ed25519 SSH keypair. Use a descriptive label so you remember what the key is for later.

When prompted for the filename, give it something obvious, for example:

```text
/Users/you/.ssh/id_ed25519_vps
```

Then verify it exists:

```bash
ls -la ~/.ssh/
```

> Confirms that your new private and public key files were created.

Display the public key so you can add it to your VPS provider or later paste it onto the server:

```bash
cat ~/.ssh/id_ed25519_vps.pub
```

> Prints the public half of your SSH key. This is safe to share. Never share the private key file.

### Getting your key onto the server: two paths

How your SSH key reaches the server depends on your VPS provider.

**Path A: provider lets you add the key before deployment.** Some providers (such as 1984.hosting and many others) let you paste your public key into their control panel before you spin up the VPS. If this option is available, use it. The server will be created with your key already installed for the root account, which means you can log in immediately with key-based authentication and never need to enable password login at all. This is the cleaner and more secure path.

**Path B: provider does not support pre-deployment keys.** Other providers only give you a root password when the VPS is created. In this case, you will need to log in with that password first, then manually add your public key to the server's `~/.ssh/authorized_keys` file (covered in Step 6 below). Once the key is in place, you disable password authentication entirely. This path works fine, but it means your server briefly accepts password logins on the open internet, which is why you should do it immediately and not leave it for later.

Either way, the end state is the same: key-only SSH access with passwords disabled.

## Step 2: first login as root

Most VPS providers give you root SSH access immediately.

Log in:

```bash
ssh root@<VPS_IP>
```

Or explicitly choose your key if needed:

```bash
ssh -i ~/.ssh/id_ed25519_vps root@<VPS_IP>
```

> Opens the first administrative session on the server. We use root only for the initial bootstrap, then move to a non-root account as quickly as possible.

If the provider gave you only a password initially, log in with it once, but do not treat that as acceptable long-term. Password-based SSH on a public VPS is an open invitation to nuisance and worse.

## Step 3: update the system immediately

Before installing anything else, bring the base system up to date.

```bash
apt update
```

> Refreshes the package lists so the server knows what current packages and security fixes are available.

```bash
apt upgrade -y
```

> Installs the latest available upgrades. On a fresh VPS, this is one of the fastest and most useful things you can do.

You are not doing this because updating is some kind of sacred ritual. You are doing it because the machine was imaged at some earlier point in time, and the internet did not politely stand still while it waited for you to log in.

## Step 4: install the basic tools you actually need

A minimal server image often omits things you will want for security, networking, and day-to-day sanity.

Install a sensible starter set:

```bash
apt install -y curl wget ufw fail2ban ca-certificates gnupg unattended-upgrades apt-transport-https software-properties-common git
```

> Installs a small baseline toolkit:
>
> - `curl` and `wget` for fetching files
> - `ufw` for firewall management
> - `fail2ban` for banning repeated authentication abuse
> - `ca-certificates` and `gnupg` for trusted package and key handling
> - `unattended-upgrades` for automatic security updates
> - `apt-transport-https` and `software-properties-common` for cleaner repository management
> - `git` because sooner or later you will need it, and forgetting it is a classic waste of time

## Step 5: create a non-root admin user

Root should not be your everyday login.

Create a normal administrative user:

```bash
adduser <USERNAME>
```

> Creates a non-root account for daily administration. Give it a strong unique password even if you plan to use SSH keys only.

Add the user to the `sudo` group:

```bash
usermod -aG sudo <USERNAME>
```

> Grants the account permission to elevate privileges with `sudo` when needed.

Switch into the new account:

```bash
su - <USERNAME>
```

> Starts a login shell as your new admin user so you can finish configuration in the account you actually intend to use.

Test sudo:

```bash
sudo whoami
```

> Confirms that privilege escalation works correctly. If it prints `root`, the account is set up properly.

## Step 6: install your SSH key for the new user

Now we make sure the new account can log in via SSH key.

If your provider injected your SSH key at deployment time, it was added to the **root** account. Your new non-root user does not have it yet, so you need to set it up manually for that account. If your provider did not support pre-deployment keys, you will also need to do this for the new user.

### Set up the SSH directory structure

Create the SSH directory:

```bash
mkdir -p ~/.ssh
```

> The `-p` flag means "create parent directories as needed and do not error if the directory already exists." This command creates the `~/.ssh` directory inside your new user's home folder. OpenSSH looks in this specific directory for authentication material. Without it, key-based login for this account will not work.

Lock down the permissions:

```bash
chmod 700 ~/.ssh
```

> Sets the directory permissions to `rwx------`, meaning only the owner can read, write, or list the contents of the directory. **This is not optional.** OpenSSH will silently refuse to use keys from a `.ssh` directory with loose permissions. The `700` permission ensures that no other user on the system can peek inside your SSH directory, which is exactly the point.

### Create and secure the authorized_keys file

Create the `authorized_keys` file:

```bash
touch ~/.ssh/authorized_keys
```

> Creates the file that stores public keys allowed to log in as this user. If the file already exists, `touch` simply updates its timestamp without changing the contents.

Set its permissions:

```bash
chmod 600 ~/.ssh/authorized_keys
```

> Sets the file permissions to `rw-------`, meaning only the owner can read or write the file. Again, **OpenSSH enforces this.** If the `authorized_keys` file is readable by other users (for example, `644` instead of `600`), the SSH daemon will ignore it entirely and your key login will fail with no obvious error message. The `600` permission ensures that only your user account can view or modify the list of trusted keys.

### Add your public key

Then, from your local machine, display your public key if you need to copy it again:

```bash
cat ~/.ssh/id_ed25519_vps.pub
```

Append that key to the server's `authorized_keys` file:

```bash
echo "paste-your-public-key-here" >> ~/.ssh/authorized_keys
```

> Adds your public key to the list of accepted login keys for the account. Make sure you paste the entire key as a single unbroken line. A typical Ed25519 public key looks like: `ssh-ed25519 AAAAC3Nza...long string... vps-admin-key`

Verify the file contents:

```bash
nano ~/.ssh/authorized_keys
```

> Lets you confirm the key is present as a single clean line and that nothing accidental was pasted into the file. You should see exactly one line per key, starting with `ssh-ed25519` (or `ssh-rsa` if you used an older key type). If you see line breaks in the middle of a key, it will not work — fix it before moving on.

### Quick permissions summary

For reference, here is what the correct SSH file permissions look like:

| Path | Permission | Octal | Why |
|------|-----------|-------|-----|
| `~/.ssh/` | `rwx------` | `700` | Only the owner can enter or list the directory |
| `~/.ssh/authorized_keys` | `rw-------` | `600` | Only the owner can read or edit the trusted key list |
| `~/.ssh/id_ed25519` (private key) | `rw-------` | `600` | Private key must never be readable by anyone else |
| `~/.ssh/id_ed25519.pub` (public key) | `rw-r--r--` | `644` | Public key can be world-readable (it is not secret) |

If SSH key login is not working and you cannot figure out why, incorrect permissions on these files are the most common cause.

## Step 7: harden SSH before the internet gets any more ideas

Now edit the SSH daemon configuration:

```bash
sudo nano /etc/ssh/sshd_config
```

At minimum, set or confirm the following values:

```text
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
ChallengeResponseAuthentication no
UsePAM yes
X11Forwarding no
```

> These changes do the important work:
>
> - disable remote root login
> - disable password login entirely
> - force SSH key authentication
> - reduce legacy or unnecessary authentication paths

I also strongly recommend moving SSH away from port `22`:

```text
Port 2222
```

> Changing the port is not real security on its own, but it cuts down a huge amount of low-grade automated noise. Think of it as a filter, not a shield.

Before restarting SSH, verify the config syntax:

```bash
sudo sshd -t
```

> Checks the SSH daemon configuration for syntax errors. This step is important because a broken SSH config is one of the fastest ways to lock yourself out of your own server.

Restart SSH:

```bash
sudo systemctl restart ssh
```

> Reloads the SSH daemon with the new settings.

Do **not** close your current session yet.
Open a second terminal on your local machine and test the new login first:

```bash
ssh -p 2222 <USERNAME>@<VPS_IP>
```

Or with the key explicitly specified:

```bash
ssh -i ~/.ssh/id_ed25519_vps -p 2222 <USERNAME>@<VPS_IP>
```

> Verifies that the hardened SSH configuration actually works before you destroy your only active admin session.

If this fails, fix it **before** logging out of the original root session.

## Step 8: configure a firewall the sane way

Now that SSH works on the new port, lock down the box.

First, set default policies:

```bash
sudo ufw default deny incoming
```

> Blocks all inbound traffic unless you explicitly allow it later.

```bash
sudo ufw default allow outgoing
```

> Allows outbound traffic by default so the server can still fetch updates and reach external services.

Allow your SSH port:

```bash
sudo ufw allow 2222/tcp
```

> Opens the one public management door you still need.

Enable the firewall:

```bash
sudo ufw enable
```

> Activates the firewall rules. Do this only after allowing your SSH port, otherwise you may immediately block yourself.

Check status:

```bash
sudo ufw status verbose
```

> Shows the active rules and default policy so you can verify the firewall is doing what you think it is doing.

If later you host something public, such as a website, explicitly add only the ports you need.

For example:

```bash
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```

> Opens HTTP and HTTPS only when you actually intend to serve a public web application.

This is the right mental model: block everything, then slowly reintroduce only what the system genuinely needs.

## Step 9: enable fail2ban

Even if password logins are disabled, it is still useful to auto-ban hostile IPs that keep hammering SSH.

Enable and start the service:

```bash
sudo systemctl enable --now fail2ban
```

> Starts `fail2ban` immediately and ensures it also starts on boot.

Create a local jail configuration:

```bash
sudo nano /etc/fail2ban/jail.local
```

Use something like:

```ini
[sshd]
enabled = true
port = 2222
backend = systemd
maxretry = 5
findtime = 10m
bantime = 1h
```

> Tells `fail2ban` to watch SSH authentication logs on your chosen port and temporarily ban IPs that repeatedly fail.

Restart fail2ban:

```bash
sudo systemctl restart fail2ban
```

> Applies your local jail settings.

Check status:

```bash
sudo fail2ban-client status sshd
```

> Shows whether the SSH jail is active and how many IPs are currently banned.

Yes, key-only SSH already does the important work. No, that does not make banning repeated nonsense useless.

## Step 10: enable automatic security updates

Most people are happy to talk about security right up until the part where they must keep doing it.

Automatic security updates help bridge that gap.

Enable the service if you have not already:

```bash
sudo dpkg-reconfigure -plow unattended-upgrades
```

> Walks you through enabling automatic installation of security updates.

Inspect the config:

```bash
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
```

> Lets you confirm that the security repositories for your distro are enabled.

You can also review periodic behavior here:

```bash
sudo nano /etc/apt/apt.conf.d/20auto-upgrades
```

A sensible configuration is:

```text
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
```

> Tells the system to refresh package lists daily and apply unattended security upgrades automatically.

Dry-run the updater:

```bash
sudo unattended-upgrades --dry-run --debug
```

> Tests the unattended-upgrades configuration without actually installing packages.

This matters because security that depends entirely on you remembering to care every single week is not really a security plan.

## Step 11: fix time, timezone, and basic OS sanity

A server with the wrong clock becomes annoying in surprisingly creative ways.

Check current time settings:

```bash
timedatectl
```

> Shows the current time, timezone, NTP status, and clock synchronization state.

Set your timezone:

```bash
sudo timedatectl set-timezone Europe/London
```

> Sets the server timezone. Replace this with your own if needed. In this example you have set it up with the London timezone.

Enable NTP time sync:

```bash
sudo timedatectl set-ntp true
```

> Ensures the server keeps its clock in sync automatically.

If you want a quick look at host identity and OS details:

```bash
hostnamectl
```

> Displays your hostname, kernel, architecture, and operating system information.

And if you want to rename the box:

```bash
sudo hostnamectl set-hostname <NEW_HOSTNAME>
```

> Changes the server hostname to something memorable and sane.

## Step 12: disable IPv6 if you are not using it

If you do not intend to use IPv6 on the VPS, disabling it can reduce a class of “I forgot that path existed” problems.

Edit sysctl settings:

```bash
sudo nano /etc/sysctl.d/99-disable-ipv6.conf
```

Add:

```ini
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
```

> Disables IPv6 system-wide. This is only appropriate if you have no need for IPv6 services.

Apply the settings:

```bash
sudo sysctl --system
```

> Reloads all sysctl configuration files so the new kernel network settings take effect.

If you use UFW, also confirm its IPv6 behavior in:

```bash
sudo nano /etc/default/ufw
```

Set:

```text
IPV6=no
```

> Prevents UFW from managing IPv6 rules if you have decided IPv6 is entirely out of scope for this server.

This is not mandatory. It is simply one of those things that helps some people sleep a little better.

## Step 13: basic file layout and permissions discipline

Do not dump production services into random home-directory folders and call it architecture.

A simple pattern that works well is:

```bash
sudo mkdir -p /opt/apps
sudo mkdir -p /var/lib/<APP_NAME>
sudo mkdir -p /etc/<APP_NAME>
```

> Creates a clearer separation between application files, persistent data, and configuration.

For example:

- `/opt/apps` for application code or unpacked binaries
- `/var/lib/<APP_NAME>` for data the service needs to keep
- `/etc/<APP_NAME>` for configuration files

Then assign ownership sensibly:

```bash
sudo chown -R <USERNAME>:<USERNAME> /opt/apps
sudo chown -R <USERNAME>:<USERNAME> /var/lib/<APP_NAME>
sudo chown -R root:root /etc/<APP_NAME>
```

> Keeps writable application/data paths owned by the service user while leaving configuration rooted where appropriate.

This is not glamorous, but good directory hygiene saves time later.

## Step 14: final checklist

Before you install the application you actually care about, the server should look something like this:

- package lists updated
- base packages upgraded
- non-root sudo user created
- SSH key authentication working
- root SSH login disabled
- password SSH disabled
- SSH moved to a non-default port or restricted to private mesh only
- firewall enabled with a deny-by-default policy
- fail2ban running
- unattended security updates enabled
- time and timezone configured
- application directories structured sensibly

That is already a much better environment than the average “I spun up a VPS and started installing stuff immediately” setup.

## Final thoughts

A VPS should not be treated like a disposable toy just because cloud panels make it feel that way.

It is a public machine on a hostile network. That sounds dramatic, but it is also just true. Once you accept that, the setup order becomes obvious. First you reduce exposure. Then you improve reliability. Then you install the fun part.

That is the boring discipline that keeps your server from becoming somebody else’s hobby.

This guide is Ubuntu and Debian based because those are still the most practical defaults for most people. There are fancier and weirder ways to do all of this. There are also worse ways, and they are usually the ones people call “simple” right before they get locked out, forget their SSH key, expose half the machine to the public web, or decide they will “set up security later.”

Do not set it up later.

Set it up first.
