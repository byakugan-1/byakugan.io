---
title: How to Self Host Your own Private Lightning Node
date: 2025-10-08 12:30:00 +0000
categories: [Guide, Privacy]
tags: [bitcoin, node, lightning, alby, ride the lightning, zeus, boltz]     # TAG names should always be lowercase
description: Learn How to setup LND, Alby Hub, Ride the Lightning and Zeus
# comments: false
# pin: true
---


## How to Self Host Your own Private Lightning Node
This guide simply shows how to set up your own lightning node stack using docker. The apps we will be working with are LND, Alby Hub, Boltz, Zeus and Ride The Lightning.

> Ensure you read and watch everything in this document

## Prerequisites
- You must be running a bitcoin core full node on ubuntu/debian
- Docker installed
- Tor installed

All of this is covered in my [node guide](https://byakugan.io/posts/node-guide).

## Setup LND Stack using docker
Firstly let's make a directory for our LND stack to live:

```bash
mkdir ~/lnd
cd ~/lnd
```

Next we must make a Docker compose file and paste in the following info:

```bash
nano docker-compose.yml
```

```yaml
services:
  lnd:
    image: lightninglabs/lnd:v0.19.3-beta # Replace with the desired version
    container_name: lnd
    restart: always
    network_mode: "host"  # Use host networking to interact with Bitcoin node directly
    command:
      - --bitcoin.mainnet
      - --bitcoin.node=bitcoind
#      - --bitcoind.rpcuser=bitcoin
#      - --bitcoind.rpcpass=bitcoin
      - --bitcoind.dir=/.bitcoin
#      - --bitcoind.rpchost=127.0.0.1
#      - --bitcoind.rpcport=8332
      - --tlsextraip=0.0.0.0
      - --tlsautorefresh
      - --tlsdisableautofill
      - --tlsextradomain=nodebox.local
      - --tor.active
      - --tor.v3
      - --configfile=/root/.lnd
      - --tor.streamisolation
      - --wallet-unlock-password-file=/root/.lnd/password.txt
      - --wallet-unlock-allow-create
      - --watchtower.active
      - --wtclient.active
    volumes:
      - ./data/lnd:/root/.lnd
      - /home/satoshi/.bitcoin:/.bitcoin:ro
      - /run/tor/control.authcookie:/run/tor/control.authcookie:ro

  rtl:
    image: shahanafarooqui/rtl:v0.15.6
    network_mode: host
    container_name: rtl
    restart: always
    stop_grace_period: 1m
    volumes:
      - ./data/rtl:/data
      - ./data/boltz:/boltz:ro
      - ./data/lnd:/lnd:ro
      - /home/satoshi/.bitcoin:/bitcoin:ro
    environment:
        # App config
        APP_PASSWORD: CREATE-SECURE-PASSWORD-FOR-BROWSER-LOGIN
        PORT: 8082
        RTL_CONFIG_PATH: "/data"
        CHANNEL_BACKUP_PATH: "/data/backup"
        LN_IMPLEMENTATION: "LND"
        BLOCK_EXPLORER_URL: "https://YOUR-MEMPOOL-URL"

        # LND connection details
        LN_SERVER_URL: "https://0.0.0.0:8080"
        MACAROON_PATH: "/lnd/data/chain/bitcoin/mainnet"
        CONFIG_PATH: "/lnd"

        # Boltz
        BOLTZ_SERVER_URL: "https://0.0.0.0:9003"
        BOLTZ_MACAROON_PATH: "/boltz/.boltz-lnd/macaroons"

  boltz:
    image: boltz/boltz-client:latest
    container_name: boltz
    network_mode: host
    restart: always
    stop_grace_period: 1m
    environment:
      HOME: /data
    volumes:
      - ./data/boltz:/data
      - ./data/lnd:/lnd:ro"
    command:
      - --datadir=/data/.boltz-lnd
      - --lnd.host=0.0.0.0
      - --lnd.macaroon="/lnd/data/chain/bitcoin/mainnet/admin.macaroon"
      - --lnd.certificate="/lnd/tls.cert"
      - --rpc.rest.host="0.0.0.0"
      - --rpc.rest.port="9003"


  albyhub:
    platform: linux/amd64
    container_name: albyhub
    restart: always
    network_mode: host
    image: ghcr.io/getalby/hub:latest
    volumes:
      - ./data/albyhub:/albyhub
      - ./data/lnd:/lnd:ro
    environment:
      - LN_BACKEND_TYPE=LND
      - LND_ADDRESS=0.0.0.0:10009
      - LND_CERT_FILE=/lnd/tls.cert
      - LND_MACAROON_FILE=lnd/data/chain/bitcoin/mainnet/admin.macaroon
      - WORK_DIR=/albyhub
      - ENABLE_ADVANCED_SETUP=false
      - PORT=8081
      - MEMPOOL_API=https://YOUR-MEMPOOL-URL/api
    stop_grace_period: 300s
```

Paste this in. You will notice that you will need to provide your own password for RTL and your own mempool.space link in the spaces provided. The LND config is pre made with the commands you can see above. This is configured to look at our /.bitcoin directory to connect to our own node, optionally you can uncomment the commands nad provide your own RPC credentials if you want to connect to your node manually or connect to a remote node. The config is set to route all traffic over tor so you must have tor installed on your machine as done in my node guide.

Next we must create a password.txt file so our LND node does not need to be manually unlocked after a power outage or system restart:

```bash
mkdir ~/lnd/data
mkdir ~/lnd/data/lnd
nano ~/lnd/data/lnd/password.txt
```

> In the password.txt file paste in a secure alphanumeric password created in your password manager, the longer the better

Next we must start the docker compose:

```bash
docker compose up -d
```

You should see all containers running:

```bash
docker ps
```

All files for each app here is in the directory ~/lnd/data

## Create Lightning Wallet
Now we must create a lightning wallet inside lnd (this is the only time this needs to be done)

```bash
docker exec -it lnd lncli create
```

> Make the password the same as the one you pasted in the password.txt above and no need for encryption in my personal opinion

> NOTE: YOU MUST KEEP A COPY OF THE SEED DISPLAYED HERE BACKED UP SECURELY, THIS IS HOW YOU WILL BE ABLE TO RECOVER YOUR FUNDS SHOULD YOU NEED TO.

Now you should see that LND is up and running and syncing gossip:

```bash
docker logs -f lnd
```

> This is how to see the lnd logs live

You will notice the logs will say ''Proxying all network traffic via Tor!'' This confirms that we are running LND in the most private way (over tor).

Let's ensure the data files have the proper permissions so you can view them:

```bash
sudo chown -R $USER:$USER ~/lnd/data/
```

## Start Using Lightning via Albyhub and/or Ride the Lightning
- You can now go on to use AlbyHub or Ride the lightning in your browser at your server's ip and port 8081 and 8082 respectively.
- I recommend AlbyHub for most people as it is very intuitive (for the most privacy do not login to an Alby account as they will prompt you to do).
- Ride the Lightning is for more advanced users and you can download your static channel backups (SCB) from here. Your Cipher seed you noted down above and the static channel backup from RTL is all you need to be backed up securely. Ensure you download an up to date copy of the SCB each time you open an new channel.
- Here is a good guide for AlbyHub and Ride the Lightning which you must watch if you are a beginner:

{% include embed/youtube.html id='8BHagtIX0rc' %}

{% include embed/youtube.html id='LnZS8k_weYY' %}

> Note: You are self-hosting Alby Hub and as long as you don't login to an alby account you are being as private as possible.

## Optional: Connect Watchtowers
You may want to use watchtowers, if so you can give other people your watchtower credentials by using this command:

```bash
docker exec -it lnd lncli tower info
```

And to connect to other watchtowers you can do:

```bash
docker exec -it lnd lncli wtclient add <address>
```

Here is more info about watchtowers and a list of watchtowers you may want to connect to:

- [Watchtower list](https://github.com/openoms/lightning-node-management/issues/4)
- [Configuring Watchtowers](https://docs.lightning.engineering/lightning-network-tools/lnd/watchtower)


Be advised that using watchtowers can reduce your privacy in exchange for offering protections against misbehaving peers. It is up to you whether you decide to use them or not.

## Linking to Zeus for mobile access over tor
Create tor HiddenService:

```bash
sudo nano /etc/tor/torrc
```

```txt
HiddenServiceDir /var/lib/tor/hs_lnd_rest/
HiddenServiceVersion 3
HiddenServicePort 8080 127.0.0.1:8080
```

Add this into your torrc file

```bash
sudo systemctl reload tor
```

> reload tor

```bash
sudo cat /var/lib/tor/hs_lnd_rest/hostname
```

> This will show your tor HiddenService address

Install lndconnect:

```bash
cd ~/downloads

wget https://github.com/LN-Zap/lndconnect/releases/download/v0.2.0/lndconnect-linux-amd64-v0.2.0.tar.gz

tar -xvf lndconnect-linux-amd64-v0.2.0.tar.gz

sudo install -m 0755 -o root -g root -t /usr/local/bin lndconnect-linux-arm64-v0.2.0/lndconnect
```

> lndconnect, created by Zap, is a utility that generates QR Code or URI to connect applications to LND

Create a lndconnect QR code:

```bash
lndconnect --host=abcd.onion --port=8080 --lnddir=~/lnd/data/lnd/
```

> Insert your own HiddenService address we generated above

- It will be a big QR code, so maximize your terminal window and use CTRL and - to shrink the code further to fit the screen
- Scan the QR in the Zeus mobile app and enjoy full mobile control over your node privately via tor.
- Here is a great Zeus guide:

{% include embed/youtube.html id='oIohVX7PeAA' %}

## Closing Thoughts
- Lightning is somewhat reckless, Always work with bitcoin you don't mind losing and familiarise yourself with everything.
- Lightning may seem like payment's won't go through, this is a common pitfall, to somewhat remedy this your channels should be about 2.5x the size of the payments you regularly expect to make. For example if you expect to send 1 million satoshis regularly your channel should be about 2.5 million satoshis in size to get reliable payments through (in my experience).