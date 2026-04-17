---
title: Gotify Notifications for FreshRSS
date: 2026-04-17 01:00:00 +0000
categories: [Projects, Self-Hosted]
tags: [freshrss,gotify,notifications,self-hosted,extensions]
description: A FreshRSS extension for sending push notifications via Gotify
# comments: false
author: vibrant
---

## Never miss an article again

I've been using [FreshRSS](https://docs.linuxserver.io/images/docker-freshrss/) as my self-hosted RSS reader for a while now, and while it's excellent at aggregating feeds, I always wanted real-time push notifications for the feeds that matter most. That's why I built a Gotify notification extension for FreshRSS.

<div style="text-align: center;">
  <img src="assets/img/gotify/demo.png" alt="Gotify FreshRSS Extension" width="350" />
</div>

## Why Gotify?

[Gotify](https://gotify.net/) is a completely self-hosted notification server. Unlike services like Pushover, Pushbullet, or ntfy.sh, Gotify gives you full control over your notification infrastructure:

- **No third-party dependencies** - Everything runs on your own hardware
- **No data leaves your network** - Your reading habits and feed selections stay private
- **Open source** - Both the server and client apps are open source
- **Simple API** - Dead simple REST API for sending messages
- **Cross-platform clients** - Available for Android, desktop, and web

If you're already self-hosting FreshRSS, adding Gotify to the mix is a natural fit for keeping your entire stack under your own control.

## What the extension does

The extension sends a push notification to your Gotify server whenever a new article appears in any feed you've selected. Key features:

- **Per-feed selection** - Choose exactly which feeds trigger notifications using a clean, collapsible UI grouped by category
- **Configurable priority** - Set notification priority (0-10) to control urgency in Gotify clients
- **Test button** - Verify your configuration with a single click
- **Detailed logging** - Human-readable logs show exactly which articles triggered notifications and any errors that occur
- **Token privacy** - Gotify tokens are always masked in log output

## Installation

1. Download the extension from [GitHub](https://github.com/byakugan-1/gotify-freshrss-extension)
2. Copy the `xExtension-Gotify` folder to your FreshRSS `extensions` directory
3. Enable the extension in FreshRSS under **Extensions**
4. Enter your Gotify server URL and application token
5. Select which feeds should trigger notifications

## Source code

The full source code is available on GitHub: [byakugan-1/gotify-freshrss-extension](https://github.com/byakugan-1/gotify-freshrss-extension)

The extension is released under the MIT License. Contributions and issues are welcome.
