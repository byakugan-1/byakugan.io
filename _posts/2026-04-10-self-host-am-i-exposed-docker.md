---
title: Self-Hosting am-i.exposed with Docker Compose
date: 2026-04-10 19:00:00 +0000
categories: [Guide, Privacy]
tags: [bitcoin,privacy,self-hosting,docker,docker-compose,mempool,tor,umbrel]
description: Run am-i.exposed yourself with Docker Compose using either prebuilt images or a local source build
# comments: false
pin: true
author: vibrant
---

Self-hosting software manually is one of those things that sounds more intimidating than it really is. The actual work is usually quite ordinary: read the project, understand what it expects, wire a few containers together, and stop pretending you need an app store for every small web service.

That is especially true for [`am-i.exposed`](../am-i-exposed/README.md), which is not some sprawling backend monster. It is a static web application that talks to a mempool-compatible API and, for some requests, a small Tor sidecar. Once you understand that, the deployment model becomes much less mysterious.

In this guide, I am going to assume two things are already true:

- you already run a Bitcoin node elsewhere;
- you already run a working [`mempool.space`](../am-i-exposed/docs/mempool-self-hosted-differences.md) stack elsewhere.

So this is **not** a guide for building the entire Bitcoin backend from scratch. It is only about hosting [`am-i.exposed`](../am-i-exposed/README.md) itself manually with Docker Compose.

You will get two paths:

1. use the prebuilt Umbrel-oriented images with your own [`docker-compose.yml`](../am-i-exposed/docs/deploy-umbrel.md);
2. clone the repo, add a compose file at the root, and build it yourself with [`docker compose up -d --build`](../am-i-exposed/docker-compose.test.yml).

## Why manual deployment is perfectly reasonable here

The important thing to understand is that [`am-i.exposed`](../am-i-exposed/next.config.ts) is built as a static export. The frontend is compiled ahead of time, then served by nginx. The repo’s Umbrel image does exactly that in [`Dockerfile.umbrel`](../am-i-exposed/Dockerfile.umbrel): it builds the site with Node and [`pnpm build`](../am-i-exposed/package.json), then copies the exported [`out`](../am-i-exposed/docker-compose.test.yml) directory into an nginx image.

That means you are not dealing with a complicated runtime application server.

You mainly need:

- one web container serving the static site;
- access to a mempool-compatible API;
- one small Tor proxy sidecar for requests that are meant to go out over Tor.

The nginx routing template in [`umbrel/nginx.conf.template`](../am-i-exposed/umbrel/nginx.conf.template) makes this explicit:

- [`/api/`](../am-i-exposed/umbrel/nginx.conf.template) is proxied to your mempool backend;
- [`/signet/api/`](../am-i-exposed/umbrel/nginx.conf.template) and [`/testnet4/api/`](../am-i-exposed/umbrel/nginx.conf.template) are rewritten and proxied the same way;
- [`/tor-proxy/`](../am-i-exposed/umbrel/nginx.conf.template) is proxied to the Tor sidecar;
- the frontend itself is served as static files.

Once you see that, the two deployment options are really just two ways of arriving at the same result.

## A quick note on self-hosted mempool backends

Before pasting compose files blindly, it is worth mentioning one caveat from [`docs/mempool-self-hosted-differences.md`](../am-i-exposed/docs/mempool-self-hosted-differences.md): not every self-hosted mempool stack behaves exactly like the public hosted instance.

The project documentation notes that if you want the closest behaviour, you should prefer:

- Bitcoin Core with [`txindex=1`](../am-i-exposed/docs/mempool-self-hosted-differences.md);
- [`mempool/electrs`](../am-i-exposed/docs/mempool-self-hosted-differences.md) rather than [`romanz/electrs`](../am-i-exposed/docs/mempool-self-hosted-differences.md);
- MariaDB for the mempool backend.

That does not mean your existing setup is automatically wrong. It just means that if something feels slightly off compared with the hosted version, the cause may be your mempool backend rather than [`am-i.exposed`](../am-i-exposed/README.md) itself.

> If your mempool instance is already working properly for your own needs, start with it as-is. Only go hunting for deeper stack differences if you notice missing behaviour.
{: .prompt-tip }

## Option 1: use the prebuilt images

This is the easiest manual path if you do not want to build anything locally.

The deployment documentation in [`docs/deploy-umbrel.md`](../am-i-exposed/docs/deploy-umbrel.md) shows that the project publishes two relevant images:

- [`ghcr.io/copexit/am-i-exposed-umbrel`](../am-i-exposed/docs/deploy-umbrel.md)
- [`ghcr.io/copexit/am-i-exposed-tor-proxy`](../am-i-exposed/docs/deploy-umbrel.md)

Despite the naming, you do not need to run Umbrel itself to use them. You can run the same images with plain Docker Compose, as long as you provide the environment variables expected by the nginx template.

### Create a directory for your deployment

On your server, create a dedicated directory for this stack:

```bash
mkdir -p ~/am-i-exposed
cd ~/am-i-exposed
```

Then create a [`docker-compose.yml`](../am-i-exposed/docs/deploy-umbrel.md) file like this:

```yaml
services:
  web:
    image: ghcr.io/copexit/am-i-exposed-umbrel:v0.2.0
    container_name: am-i-exposed-web
    restart: unless-stopped
    ports:
      - "3080:8080"
    environment:
      APP_MEMPOOL_IP: 192.168.1.50
      APP_MEMPOOL_PORT: "4080"
      APP_TOR_PROXY_IP: tor-proxy
      APP_TOR_PROXY_PORT: "3001"
      APP_MEMPOOL_HIDDEN_SERVICE: ""
    depends_on:
      - tor-proxy

  tor-proxy:
    image: ghcr.io/copexit/am-i-exposed-tor-proxy:v0.2.0
    container_name: am-i-exposed-tor-proxy
    restart: unless-stopped
    environment:
      PORT: "3001"
      TOR_PROXY_IP: 127.0.0.1
      TOR_PROXY_PORT: "9050"
```

A few points matter here:

- [`APP_MEMPOOL_IP`](../am-i-exposed/umbrel/nginx.conf.template) should point to the host or container name of your existing mempool backend;
- [`APP_MEMPOOL_PORT`](../am-i-exposed/umbrel/nginx.conf.template) should be the HTTP port exposed by that backend;
- [`APP_TOR_PROXY_IP`](../am-i-exposed/umbrel/nginx.conf.template) points to the sidecar service name, which is why [`tor-proxy`](../am-i-exposed/docker-compose.test.yml) works;
- [`APP_TOR_PROXY_PORT`](../am-i-exposed/umbrel/nginx.conf.template) stays at `3001` unless you intentionally change the sidecar configuration;
- [`APP_MEMPOOL_HIDDEN_SERVICE`](../am-i-exposed/docker-compose.test.yml) can stay blank unless you specifically want to surface a hidden service hostname.

The overall structure is based on the same pattern shown in [`docker-compose.test.yml`](../am-i-exposed/docker-compose.test.yml) and the compose example in [`docs/deploy-umbrel.md`](../am-i-exposed/docs/deploy-umbrel.md).

### Bring the stack up

Start it with:

```bash
docker compose up -d
```

Then check logs if needed:

```bash
docker compose logs -f web
docker compose logs -f tor-proxy
```

At that point, the site should be available on port `3080` of your server, unless you changed the mapping.

If you already run a reverse proxy such as nginx, Caddy, or Traefik in front of your services, you can skip publishing port `3080` publicly and instead put this stack behind your normal internal reverse proxy arrangement.

> Do not expose random services directly to the internet just because Docker made it easy. If this is internet-facing, think about TLS, access policy, updates, and whether public exposure is even necessary.
{: .prompt-warning }

## Option 2: clone the repo and build it yourself

This path is for people who would rather build the site from source and not depend on prebuilt images.

The repo already gives away most of the model:

- [`Dockerfile.umbrel`](../am-i-exposed/Dockerfile.umbrel) builds the frontend and serves the exported site with nginx;
- [`docker-compose.test.yml`](../am-i-exposed/docker-compose.test.yml) shows how the exported [`out`](../am-i-exposed/docker-compose.test.yml) directory can be served through nginx with the same routing template;
- [`umbrel/tor-proxy/Dockerfile`](../am-i-exposed/umbrel/tor-proxy/Dockerfile) builds the Tor sidecar.

So the clean manual method is:

1. clone the repo;
2. add your own [`docker-compose.yml`](../am-i-exposed/docker-compose.test.yml) at the repo root;
3. let Compose build both the web image and the Tor sidecar locally.

### Clone the repository

```bash
git clone https://github.com/copexit/am-i-exposed.git
cd am-i-exposed
```

### Create a repo-root compose file

At the root of the cloned repo, create [`docker-compose.yml`](../am-i-exposed/docker-compose.test.yml) with content like this:

```yaml
services:
  web:
    build:
      context: .
      dockerfile: Dockerfile.umbrel
    container_name: am-i-exposed-web
    restart: unless-stopped
    ports:
      - "3080:8080"
    environment:
      APP_MEMPOOL_IP: 192.168.1.50
      APP_MEMPOOL_PORT: "4080"
      APP_TOR_PROXY_IP: tor-proxy
      APP_TOR_PROXY_PORT: "3001"
      APP_MEMPOOL_HIDDEN_SERVICE: ""
    depends_on:
      - tor-proxy

  tor-proxy:
    build:
      context: ./umbrel/tor-proxy
    container_name: am-i-exposed-tor-proxy
    restart: unless-stopped
    environment:
      PORT: "3001"
      TOR_PROXY_IP: 127.0.0.1
      TOR_PROXY_PORT: "9050"
```

Then start it exactly as requested:

```bash
docker compose up -d --build
```

This tells Compose to:

- build the main image from [`Dockerfile.umbrel`](../am-i-exposed/Dockerfile.umbrel);
- build the sidecar from [`umbrel/tor-proxy/Dockerfile`](../am-i-exposed/umbrel/tor-proxy/Dockerfile);
- create and start both containers in the background.

### Why this works

The build flow is straightforward:

- the main image installs dependencies from [`package.json`](../am-i-exposed/package.json) and [`pnpm-lock.yaml`](../am-i-exposed/pnpm-lock.yaml);
- it runs [`pnpm build`](../am-i-exposed/package.json), which in turn runs the site build and exports static output;
- nginx then serves the generated files and proxies API requests according to [`umbrel/nginx.conf.template`](../am-i-exposed/umbrel/nginx.conf.template).

In other words, you are not improvising some unsupported setup. You are simply using the project’s own container logic outside Umbrel.

## Which option should you choose?

Use the prebuilt image path if:

- you want the simplest deployment;
- you are comfortable trusting the published images;
- you want faster updates with less local build time.

Use the local build path if:

- you want to inspect or modify the code yourself;
- you prefer not to rely on prebuilt images;
- you want to pin your deployment directly to a cloned git revision and rebuild on your own schedule.

Neither approach is inherently magical. One is more convenient. The other is slightly more sovereign. Pick the trade-off you actually value instead of performing ideology for strangers online.

## Updating the deployment

This part matters more than people admit. Self-hosting is easy to start and boring to maintain, which is exactly why maintenance gets neglected.

The right update process depends on which option you chose.

### Updating a prebuilt-image deployment

If you used published GHCR images, the workflow is simple:

```bash
docker compose pull
docker compose up -d
```

If you want to force recreation cleanly, you can do:

```bash
docker compose pull
docker compose up -d --force-recreate
```

This pulls newer tags and recreates the containers using the updated images.

The important subtlety is version pinning. If your compose file hard-pins a tag such as `v0.2.0`, then nothing changes until you edit the tag yourself. That is usually the correct behaviour. Blindly floating to `latest` is convenient right up until it becomes annoying.

> Pin explicit image tags and update them deliberately. Predictable maintenance beats surprise breakage.
{: .prompt-info }

### Updating a local-build deployment

If you cloned the repo and build locally, the workflow is usually:

```bash
git pull
docker compose up -d --build
```

That fetches upstream changes, rebuilds the images locally, and recreates the containers.

If you want a slightly cleaner sequence:

```bash
git pull
docker compose build --no-cache
docker compose up -d
```

You probably do not need [`--no-cache`](../am-i-exposed/Dockerfile.umbrel) every single time, but it is useful when you suspect cached layers are hiding a change.

### What to verify after updates

After either kind of update, check a few boring things instead of assuming all is well:

- does the site load correctly;
- do requests under [`/api/`](../am-i-exposed/umbrel/nginx.conf.template) still reach your mempool backend;
- does the Tor sidecar still answer health and proxy traffic correctly;
- do browser dev tools show obvious failing requests.

You can also inspect logs:

```bash
docker compose logs --tail=100 web
docker compose logs --tail=100 tor-proxy
```

## A few deployment notes worth keeping in your head

### The frontend is static, but the routing still matters

Because the site is static, some people may look at this and think they can just throw the exported files behind any generic web server and call it done. Not quite.

The nginx configuration is doing more than serving files. It also proxies the API and Tor routes, and it sets sensible cache behaviour for the frontend in [`umbrel/nginx.conf.template`](../am-i-exposed/umbrel/nginx.conf.template).

If you replace that layer, you need to reproduce the same routing behaviour yourself.

### The test compose file is a clue, not the final answer

[`docker-compose.test.yml`](../am-i-exposed/docker-compose.test.yml) is useful because it shows the intended wiring pattern, but it uses a mock mempool proxy for testing. In a real deployment, you should point the web container at your actual mempool instance instead.

### Tor still matters here

The Tor sidecar is not decorative. The deployment docs in [`docs/deploy-umbrel.md`](../am-i-exposed/docs/deploy-umbrel.md) make clear that some checks are intentionally routed through it. If you remove it, expect some functionality or privacy assumptions to break.

## Final thoughts

There is nothing exotic about hosting [`am-i.exposed`](../am-i-exposed/README.md) yourself. It is a static frontend, an nginx routing layer, a mempool-compatible backend, and a small Tor sidecar. That is all very manageable.

If you want the fastest path, use the prebuilt images.
If you want the most independent path, clone the repo and build it yourself.

Both approaches are valid. The only bad option is pretending you need a branded dashboard to run software that is already simple enough to understand on its own.
