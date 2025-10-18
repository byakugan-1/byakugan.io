---
title: Linux VPS Setup and Hardening Guide
date: 2025-10-08 12:30:00 +0000
categories: [Guide, Privacy]
tags: [vps,security,mynymbox,hosting,server]     # TAG names should always be lowercase
description: Learn to securely set up and harden a Virtual Private Server (VPS)
# comments: false
image: /assets/img/linux-vps-guide-images/header.png
---

## Buy a VPS privately

If you value privacy, choose providers that minimize data collection and support privacy-preserving payments:
- [1984.hosting](https://1984.hosting)
- [mynymbox.net](https://mynymbox.net)

Prefer paying with Bitcoin after a Whirlpool coinjoin, Bitcoin via Lightning, or Monero. These methods reduce linkage between your identity and your server. When possible, avoid providing personally identifying information during signup.

## Choosing a Linux distro for your VPS (quick guide)

- Ubuntu (Debian-based)
  - Pros: beginner-friendly, broad package support, many tutorials, frequent updates
  - Cons: more default services running, therefore more bloated
- Debian (independent)
  - Pros: very stable, lightweight baseline, ideal for long-running or low-RAM servers
  - Cons: older packages by default, more manual setup early on
- Alpine (independent)
  - Pros: extremely lightweight, fast boot, good security posture
  - Cons: not beginner-friendly, more manual configuration, no systemd, smaller community
- Fedora (Red Hat family)
  - Pros: cutting-edge stack, SELinux by default, strong dev tooling
  - Cons: faster-moving base can require more maintenance and frequent updates

Rule of thumb: Ubuntu LTS or Debian Stable are solid defaults. If RAM is tight, lighter distros help.

## Setup and Hardening guide

### 1) Inspect local SSH keys

```bash
ls -la ~/.ssh/
```

> Lists your local SSH key files and permissions. You’ll use public keys to log
> in to the VPS; keep private keys protected with a strong passphrase.

```bash
rm ~/.ssh/<name>
```

> Removes an old or unused key file. Do this only if you’re sure you won’t need
> it. Rotate keys rather than leaving stale credentials around.

```bash
cat ~/.ssh/*.pub
```

> Displays all public keys. These are safe to share with your VPS provider or
> add to the server’s authorized_keys file.

```bash
cat ~/.ssh/<name>.pub
```

> Shows a specific public key. Use the key that matches the private key you plan
> to use for login.

### 2) Generate SSH key for your VPS provider (local)

```bash
ssh-keygen
```

> Generates a new SSH keypair interactively. Choose ed25519 when prompted and
> set a long passphrase. Store the passphrase in a password manager.

```bash
~/.ssh/<SSH_KEY_NAME>
```

> This is the typical location for SSH keys. Replace `<SSH_KEY_NAME>` with a
> clear name (e.g., id_ed25519_vps) to keep your keys organized.

```bash
ls -la ~/.ssh/
```

> Verifies that your new keys exist and have appropriate permissions.

```bash
cat ~/.ssh/<SSH_KEY_NAME>.pub
```

> Displays your public key. Add this to your VPS provider before creating the
> server so you can log in without passwords.

### 3) First login and initial updates

```bash
ssh root@<VPS_IP>
```

> Logs in to the new server as root using your provider-injected key. Use this
> only for the initial setup; you’ll create a non-root admin shortly.

```bash
ssh -i ~/.ssh/<SSH_KEY_NAME> root@<VPS_IP>
```

> Explicitly selects your private key file if your agent has multiple keys
> loaded or the default isn’t correct.

```bash
apt update
```

> Refreshes the package index so your system knows about the latest available
> updates.

```bash
apt upgrade -y
```

> Upgrades installed packages to their newest versions. Security patches often
> arrive via these updates.

```bash
apt install unattended-upgrades
```

> Installs the tool that automatically applies security updates.

```bash
dpkg-reconfigure -plow unattended-upgrades
```

> Enables unattended security updates through a guided configuration. Choose to
> apply security fixes automatically.

```bash
nano /etc/apt/apt.conf.d/50unattended-upgrades
```

> Open the config and ensure “-security” origin is allowed. Consider enabling
> automatic reboots during a maintenance window (e.g., 02:00) to apply kernel
> fixes safely.

```bash
unattended-upgrades --dry-run --debug
```

> Performs a dry run to confirm that automatic updates are configured as
> intended, without making changes.

### 4) Create a non-root admin and verify sudo

```bash
adduser <USERNAME>
```

> Creates your daily admin account. Use a strong unique password even if you
> primarily log in via SSH keys.

```bash
usermod -aG sudo <USERNAME>
```

> Grants sudo privileges to your admin account for controlled elevation.

```bash
su - <USERNAME>
```

> Switches to your new account so you can test its environment and permissions.

```bash
sudo whoami
```

> Confirms sudo works (should print “root”). After this, avoid direct root
> logins and use your admin account with sudo.

### 5) Configure SSH key authentication and harden SSH

```bash
ssh-keygen -t ed25519 -C "<KEY_LABEL>"
```

> Generates a modern local key (if you haven’t already). The label helps you
> track purpose or ownership of the key.

```bash
mkdir -p ~/.ssh
```

> Ensures the SSH directory exists on the server for your admin user.

```bash
chmod 700 ~/.ssh
```

> Sets strict permissions so OpenSSH will accept the directory.

```bash
touch ~/.ssh/authorized_keys
```

> Creates the authorized_keys file for your admin user.

```bash
chmod 600 ~/.ssh/authorized_keys
```

> Sets strict permissions on the authorized_keys file to avoid login failures.

```bash
cat ~/.ssh/id_ed25519.pub
```

> Shows your local public key. You’ll paste this into the server’s
> authorized_keys.

```bash
echo "paste-your-public-key-here" >> ~/.ssh/authorized_keys
```

> Appends your public key to the server’s authorized_keys. Only paste the
> “.pub” line, not the private key.

```bash
nano ~/.ssh/authorized_keys
```

> Verify the key was added correctly and is on a single line. Remove duplicates
> or stale keys.

```bash
nano ~/.ssh/config
```

> Optional: create a local SSH profile. Example:
>
> Host vps-server  
>     HostName <VPS_IP>  
>     User <USERNAME>  
>     IdentityFile ~/.ssh/id_ed25519

```bash
sudo nano /etc/ssh/sshd_config
```

> Open the SSH daemon config to harden access. Disallow root logins and disable
> password authentication to force key-based access.

```bash
PermitRootLogin no
```

> Prevents logins as root over SSH. Always log in as your admin user and use
> sudo for privilege escalation.

```bash
PasswordAuthentication no
```

> Disables password-based SSH logins. This removes a major brute-force attack
> vector and ensures keys are required.

```bash
UsePAM no
```

> Disables PAM modules for SSH. If you later need PAM features (e.g., 2FA),
> re-enable thoughtfully.

```bash
ChallengeResponseAuthentication no
```

> Disables legacy interactive auth mechanisms, further tightening the SSH
> surface.

```bash
sudo cat /etc/ssh/sshd_config.d/50-cloud-init.conf
```

> Checks for cloud-init overrides that might conflict with your hardening.

```bash
sudo cat /etc/ssh/sshd_config.d/60-cloudimg-settings.conf
```

> Reviews additional layered settings; align them with your sshd_config.

```bash
sudo nano /etc/ssh/sshd_config.d/50-cloud-init.conf
```

> Edit cloud-init drop-in config if needed to ensure passwords and root logins
> remain disabled.

```bash
sudo nano /etc/ssh/sshd_config.d/60-cloudimg-settings.conf
```

> Update any defaults that re-enable weak SSH settings. Keep your original
> session open to avoid lockouts.

```bash
sudo systemctl restart ssh
```

> Applies the SSH changes. Test a new SSH session in another terminal before
> closing the original one, so you can revert safely if needed.

### 6) Configure firewall (UFW)

```bash
sudo apt install ufw
```

> Installs Ubuntu’s uncomplicated firewall. It’s a simple interface for default-
> deny inbound policies and explicit allowed ports.

```bash
sudo ufw default deny incoming
```

> Blocks unsolicited inbound traffic. You will explicitly allow only what you
> need.

```bash
sudo ufw default allow outgoing
```

> Allows outbound traffic by default so your server can reach package mirrors
> and APIs.

```bash
sudo ufw allow ssh
```

> Opens the SSH port so you don’t lock yourself out. If you later change your
> SSH port, adjust this rule accordingly.

```bash
sudo ufw allow 80/tcp
```

> Opens HTTP. Add only if you run a web server or reverse proxy.

```bash
sudo ufw allow 443/tcp
```

> Opens HTTPS. Add only if you need encrypted web access.

```bash
sudo ufw enable
```

> Activates the firewall with the current rules. Confirm access from a second
> terminal before closing your session.

```bash
sudo ufw status verbose
```

> Displays active rules and policies so you can confirm the firewall is in the
> intended state.

### 7) Install and configure Fail2ban

```bash
sudo apt install fail2ban
```

> Installs Fail2ban, which monitors logs and temporarily bans abusive IPs.

```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

> Creates a local override file you can safely edit without changing defaults.

```bash
sudo nano /etc/fail2ban/jail.local
```

> Enable the sshd jail and configure thresholds (e.g., maxretry=3, bantime=3600,
> findtime=600). Add ignoreip for trusted admin IPs to avoid accidental bans.

```bash
sudo systemctl enable fail2ban
```

> Ensures Fail2ban starts automatically on boot.

```bash
sudo systemctl start fail2ban
```

> Starts the service immediately so protections take effect.

```bash
sudo fail2ban-client status
```

> Shows Fail2ban’s global status and enabled jails.

```bash
sudo fail2ban-client status sshd
```

> Displays details for the sshd jail, including currently banned IPs.

### 8) Memory checks and swap file

```bash
sudo journalctl | grep -i "out of memory"
```

> Searches logs for OOM events. Frequent OOMs indicate you should add RAM,
> reduce workloads, or add swap to buffer spikes.

```bash
sudo fallocate -l 2G /swapfile
```

> Allocates a 2G swapfile. Adjust size based on your instance; small VPS hosts
> benefit from modest swap.

```bash
sudo chmod 600 /swapfile
```

> Locks down permissions so the swapfile cannot be read by other users.

```bash
sudo mkswap /swapfile
```

> Initializes the swapfile for use by the kernel.

```bash
sudo swapon /swapfile
```

> Activates swap immediately.

```bash
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

> Makes swap persistent across reboots by adding it to fstab.

```bash
free -h
```

> Verifies available memory and swap in a human-readable format.

### 9) Verification checklist

```bash
sudo sshd -T
```

> Prints the effective SSH configuration as interpreted by the daemon, so you
> can confirm hardening settings are active.

```bash
sudo netstat -tulpn
```

> Lists listening ports and owning processes. Requires net-tools. Use this to
> confirm only intended services are exposed.

```bash
sudo ufw status verbose
```

> Displays firewall policies and open ports. Cross-check with netstat to ensure
> consistency.

```bash
sudo fail2ban-client status
```

> Confirms Fail2ban is running and shows enabled jails.

```bash
sudo journalctl -fu sshd
```

> Tails SSH logs in real time. Test a login and watch entries to verify behavior
> and investigate unexpected errors.

### 10) Regular maintenance tasks

```bash
sudo apt update
```

> Refreshes package indexes so you see the latest available updates.

```bash
sudo apt upgrade
```

> Applies updates. Schedule this weekly at minimum; more frequent for exposed
> services.

```bash
sudo tail -f /var/log/auth.log
```

> Watches authentication logs in real time. Detects login attempts and unusual
> activity quickly.

```bash
sudo tail -f /var/log/fail2ban.log
```

> Monitors Fail2ban’s actions and alerts so you know when IPs are being banned
> or unbanned.

```bash
sudo tail -f /var/log/ufw.log
```

> Streams firewall logs to surface unexpected inbound hits or misconfigurations.

## Security best practices

1. Store SSH private keys securely with strong passphrases; back them up safely.
2. Keep an inventory of authorized users and keys; remove stale accounts.
3. Default-deny inbound with UFW; only open required ports.
4. Rotate SSH keys periodically, especially when team membership changes.
5. Monitor system resources, disk usage, and logs; set alerts for anomalies.
6. Apply security updates promptly; unattended security upgrades help.
7. Document custom ports and changes for easy auditing and troubleshooting.
8. Prefer privacy-preserving VPS providers (e.g., 1984.hosting, mynymbox.net).
9. Pay with Bitcoin after a Whirlpool coinjoin, Bitcoin via Lightning, or Monero
   to reduce identity linkage to your server.

## Useful command list for beginners

System and OS information:

```bash
uname -a
```

> Shows kernel and basic system info; useful for confirming environment.

```bash
hostnamectl
```

> Displays and can set the system’s hostname and OS metadata.

```bash
uptime
```

> Tells how long the server has been running and current load averages.

```bash
lsb_release -a
```

> Prints distro details (if lsb-release is installed) for quick identification.

```bash
cat /etc/os-release
```

> Shows OS name and version from the standard release file.

```bash
lscpu
```

> Describes CPU model, cores, architecture—handy for sizing workloads.

Hardware and usage:

```bash
lspci
```

> Lists PCI devices. In VPS, often minimal; more relevant on bare metal.

```bash
lsusb
```

> Lists USB devices. In VPS, commonly empty; useful on physical hosts.

```bash
free -h
```

> Shows memory usage with human-readable units.

```bash
df -h
```

> Displays disk usage by filesystem, highlighting space pressure.

```bash
du -sh <directory>
```

> Summarizes the size of a directory. Helps find large folders quickly.

Users and groups:

```bash
whoami
```

> Prints your current username; confirms which account you’re operating under.

```bash
id
```

> Shows user and group IDs as well as group memberships.

```bash
adduser <user>
```

> Creates a user interactively with home directory and default settings.

```bash
usermod -aG sudo <user>
```

> Adds a user to the sudo group for privilege escalation.

```bash
passwd <user>
```

> Changes a user’s password. Use for initial setup or urgent rotation.

```bash
deluser <user>
```

> Removes a user. Clean up accounts that no longer need access.

```bash
groups
```

> Lists groups the current user is a member of.

```bash
groupadd <group>
```

> Creates a new group, useful for organizing privileges.

```bash
gpasswd -a <user> <group>
```

> Adds a user to a group via gpasswd (group password management).

```bash
last
```

> Shows login history, useful for detecting unusual access patterns.

```bash
w
```

> Shows who is logged in and their current activity.

Files and directories:

```bash
ls -l
```

> Lists files with permissions, ownership, and timestamps.

```bash
ls -a
```

> Includes hidden files (dotfiles) in listings.

```bash
cd <directory>
```

> Changes the current directory.

```bash
pwd
```

> Prints the present working directory.

```bash
mkdir <directoryname>
```

> Creates a new directory.

```bash
rmdir <directoryname>
```

> Removes an empty directory; fails if it contains files.

```bash
rm -rf <directory>
```

> Recursively deletes a directory and all contents. Use with caution.

```bash
cp <file1> <file2>
```

> Copies a file. Use -r for directories.

```bash
mv <file1> <file2>
```

> Moves or renames a file or directory.

```bash
touch <file.txt>
```

> Creates an empty file or updates its timestamp.

```bash
nano <file.txt>
```

> Opens a file in the nano editor for quick edits.

```bash
cat <file.txt>
```

> Prints file contents to the terminal.

```bash
less <file.txt>
```

> Views a file with paging; search with “/pattern”, quit with “q”.

```bash
head -n 20 <file.txt>
```

> Shows the first 20 lines of a file; adjust the number as needed.

```bash
tail -f /var/log/syslog
```

> Streams a log in real time; great for debugging and monitoring.

Search and size:

```bash
find / -name "<name>.<file-type>"
```

> Searches by name and extension, starting at root. May be slow; narrow paths
> for performance.

Networking:

```bash
ip a
```

> Shows network interfaces and IP addresses; confirms connectivity config.

```bash
ifconfig
```

> Legacy interface listing (requires net-tools). Use “ip” in modern setups.

```bash
ping <website>
```

> Checks basic connectivity and round-trip latency.

```bash
traceroute <website>
```

> Reveals the path packets take; useful for routing issues.

```bash
nslookup <website>
```

> Queries DNS for a domain; confirm resolution and records.

```bash
dig <website>
```

> Detailed DNS query tool; shows record TTLs and authoritative servers.

```bash
netstat -tulnp
```

> Lists listening ports and processes (requires net-tools). Alternative: “ss”.

```bash
ss -tulnp
```

> Modern tool to list sockets/ports without needing net-tools.

```bash
curl <URL>
```

> Fetches URL content; great for testing endpoints and APIs.

```bash
wget <URL>
```

> Downloads files over HTTP/HTTPS/FTP.

```bash
scp file user@server:/directory
```

> Copies files over SSH; secure alternative to FTP.

```bash
rsync -av file user@server:/directory
```

> Synchronizes files/directories efficiently; preserves attributes with “-a”.

```bash
ftp <server>
```

> Connects to FTP servers (not recommended for secure workflows).

```bash
telnet host <port>
```

> Tests TCP connectivity to a specific port; useful for quick checks.

Packages (Debian/Ubuntu):

```bash
apt update
```

> Refreshes package lists; run before installing/upgrading.

```bash
apt upgrade
```

> Upgrades packages; consider “-y” for automation with care.

```bash
apt full-upgrade
```

> Upgrades including dependencies that may remove/install packages.

```bash
apt install <package>
```

> Installs a package from the repositories.

```bash
apt remove <package>
```

> Uninstalls a package but leaves config files.

```bash
apt purge <package>
```

> Removes a package and its config files for a clean slate.

```bash
apt autoremove
```

> Cleans up unused dependencies after removals.

```bash
apt dpkg -i <file.deb>
```

> Installs a local .deb file; use with caution and verify signatures.

```bash
dpkg -l
```

> Lists installed packages; useful for audits and troubleshooting.

Permissions:

```bash
chmod 755 <file>
```

> Sets file permissions (rwx for owner, rx for group/others). Choose carefully.

```bash
chown <user>:<group> <file>
```

> Changes file ownership to the specified user and group.

```bash
chgrp <group> <file>
```

> Changes a file’s group ownership.

```bash
umask 022
```

> Sets default permission mask for new files. Adjust to tighten defaults.

```bash
stat <file>
```

> Shows detailed metadata and permissions for a file.

Extras:

```bash
alias ll='ls -la'
```

> Sets a handy alias for detailed listings.

```bash
history
```

> Displays your shell command history.

```bash
!<number>
```

> Re-runs a specific command from history by its number.

```bash
clear
```

> Clears the terminal screen for readability.

```bash
echo "Hello"
```

> Prints a string to the terminal; useful for quick tests.

```bash
date
```

> Shows the current date and time.

```bash
cal
```

> Displays a calendar.

```bash
shutdown -h now
```

> Shuts down the system immediately and halts power.

```bash
reboot
```

> Restarts the system.

```bash
timedatectl
```

> Shows or sets time and timezone; ensure correct time for TLS and logs.

```bash
crontab -e
```

> Edits scheduled cron jobs for the current user.

## Closing thoughts

This baseline focuses on strong, simple controls: SSH keys only, no root logins, default-deny firewall, abuse detection with Fail2ban, and routine updates. 

This should help you get up and running with your first VPS securely.

Always remember to do your own research!