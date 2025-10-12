---
title: One Maintainer, Many Risks — Why you should run Core over Knots
date: 2025-09-20 12:30:00 +0000
categories: [Blog, Bitcoin]
tags: [bitcoin, bitcoin knots, analysis]     # TAG names should always be lowercase
description: Knots promises cleaner mempools, but policy filters dont stop on-chain spam - and they break privacy. Here's why Bitcoin Core remains the pragmatic choice.
# comments: false
pin: true
---

Your node is your lens into Bitcoin. Choose the wrong lens, and you’re not just seeing a distorted picture - you’re accepting hidden tradeoffs in security, privacy, and reliability. With rising frustration over “spam” and shifting policy in Bitcoin Core, some are eyeing Bitcoin Knots for stricter filters. But mempool policy isn’t a spam filter, and running Knots comes with real governance and privacy downsides. If your goal is digital sovereignty, you need to understand those tradeoffs clearly.

> Client choice affects your security, privacy, and reliability. It's not a cosmetic preference.
{: .prompt-warning }

## The stakes: client choice is a sovereignty decision
Your Bitcoin client isn’t a passive piece of software; it’s the arbiter of what you see, what you relay, how quickly you receive fixes, and how much trust you must place in others. For people who value autonomy and user sovereignty, that choice needs to be informed by how the software is built, who maintains it, and what it actually changes.

Interest in Bitcoin Knots has spiked because Core is moving to adjust/deprecate certain standardness and relay-policy knobs (like datacarriersize). Some hope tighter default policies will discourage “spam” and inscriptions. The impulse is understandable. But sovereignty isn’t just about flipping a stricter switch; it’s about choosing systems with robust governance, peer review, and behavior that matches your goals in the real world. This piece lays out what Knots is, what it isn’t, how its policies actually behave on today’s network, and why Core remains the pragmatic, privacy-preserving default right now.

## What Bitcoin Knots is (and isn’t)
Bitcoin Knots is not a separate implementation like btcd or libbitcoin. It is a lightly 'patched' fork of Bitcoin Core. In practice, almost all of Knots’ code is upstream Core; Knots adds or modifies a small set of patches chosen by its maintainer. That means Knots inherits Core’s codebase - and depends on Core for the vast majority of security fixes, bug fixes, and improvements.

This dependency comes with timing risk. In public release histories, Knots typically lags Core by anywhere from about a week to multiple months. When Core ships a critical fix, a lagging fork creates an exposure window for users who rely on it. That’s not hypothetical; it’s a predictable consequence of forking a relatively fast-moving upstream. If you choose Knots, you’re effectively choosing Core plus a delay and a few controversial patches.

> Fork lag creates exposure windows. If Core ships a critical fix, downstream delays increase your risk until Knots catches up.
{: .prompt-danger }

## Governance risk: one maintainer is one failure domain
Open-source security is not just about code quality; it’s about process. Bitcoin’s culture depends on distributed review, multiple maintainers, and conservative merging practices that minimize individual failure. Knots breaks from that norm. It has a single maintainer with unilateral control over merges, release cadence, and policy decisions. The public repository shows no outside pull requests being merged and minimal visible peer review on changes.

That governance structure concentrates trust. If you cannot personally audit every diff and build, you are trusting one person’s judgment, process, and keys. That is the opposite of the security-through-diversity approach Bitcoin has spent years cultivating. Even if you agree with the policy outcomes, the supply-chain risk is material: a mistake, a compromised signing key, or a rushed merge can ship straight to users without the friction of peer review.

> Single‑maintainer governance concentrates trust and increases supply‑chain risk (key compromise, rushed merges, unreviewed changes).
{: .prompt-danger }

## Track record matters
When you must trust a single maintainer, their operational security and history matter. Publicly acknowledged incidents involving Knots’ maintainer Luke Dashjr include a compromised signing key and a major theft due to coins being kept on a server that was breached. These incidents don’t imply malice, but they do highlight process and key-management shortcomings that become highly relevant when that same person is the sole gatekeeper for a node client. ***I mean why on earth was Luke not securing his 200+ bitcoin in HD hardware wallet(s) - If this life-changing oversight can be made here why would you trust this dev with your node apparatus.***

In Bitcoin, release integrity relies on careful key handling, reproducible builds, and communal verification. A maintainer’s key compromise undermines the very mechanism users depend on to know they’re running what they think they’re running. Again, the point is not to attack Luke - it’s to assess risk. If you run Knots without doing your own thorough verification, you are concentrating trust in very few people.

> Large holdings should be secured on hardware wallets; key‑management lapses and signing key compromises are red flags for software trust.
{: .prompt-warning }

## What Knots actually changes: mempool policy, not the blockchain
It’s crucial to separate consensus from policy. Consensus rules define what is valid in a block. Policy rules decide what your node relays and admits to its mempool before mining. Knots does not change consensus. If a transaction is consensus-valid and included in a valid block, a Knots node will validate and store it just like Core.

Where Knots differs is in its mempool policy. It filters more aggressively - what it will relay, accept, and verify. This has two practical consequences. First, you still end up storing inscriptions, OP_RETURNs, and other data-heavy transactions if miners include them despite filtering the transactions from your mempool. Second, your local view of the mempool diverges from the rest of the network, which can harm fee estimation and transaction timing predictions. Filtering your mempool may feel cleaner, but it doesn’t stop anything; it mostly changes what you see before it hits a block.

> Policy ≠ consensus. Stricter mempool rules change what you relay locally; they do not prevent mined transactions from being validated and stored.
{: .prompt-info }

## The anti-privacy side effect: broken workflows at default settings
There’s a second-order effect to stricter relay policy: it breaks real privacy tools that depend on standard data-carrier use. At Knots’ defaults, BIP47/PayNym notification transactions do not relay. Those notifications rely on an OP_RETURN payload of a certain size; reduce the allowed data-carrier size below that threshold and the transaction never propagates from a Knots node.

The same is true for Whirlpool Tx0 transactions at Knots’ defaults. Whether your goal is notification-based private receiving (BIP47) or coordinated coin-mixing with strong anonymity sets (Whirlpool), Knots raises unnecessary barriers for ordinary users by default. Privacy on Bitcoin is already hard. A “policy purity” posture that blocks common, non-consensus-breaking privacy workflows is a net loss for users seeking sovereignty. The costs are immediate and practical: fewer people will successfully use these tools as those who try will face confusing failures that don’t exist on Core.

> Knots’ default policies block BIP47/PayNym notifications and Whirlpool Tx0 relay; stricter data‑carrier limits break common privacy workflows.
{: .prompt-warning }

## Why mempool filters don’t stop “spam”
If the goal is to reduce on-chain “spam,” mempool-level filtering is not effective. The network is not a uniform filter where everyone must agree for a transaction to reach a miner. In practice, only a small fraction of permissive nodes is enough for transactions to find their way to miners. Once a miner includes the transaction, every node - including Knots - must accept and store it.

Mining centralization makes this even more true. Out-of-band submission channels let transactions bypass mempools entirely. Services like Marathon’s Slipstream openly route transactions straight to miners. You can filter your local mempool all you want; it won’t prevent direct miner inclusion. Empirically, we’ve also seen that even when a minority of nodes lower their relay thresholds (for example, by reducing minrelaytxfee far below default), those transactions still reach miners and are mined - even if the majority of nodes remain stricter. The lesson is simple: mempool policy primarily affects your local view. It does not meaningfully change on-chain outcomes in a world where miners can be reached via permissive peers or directly.

> Local filters don’t change miner behavior. Permissive peers and direct‑to‑miner channels (e.g., Slipstream) bypass mempools entirely.
{: .prompt-info }

## What would actually change outcomes (and the costs)
Consensus changes (soft forks): You could invalidate certain uses at the consensus layer, making them unmineable. That would “work” in the narrow sense, but it’s a profound shift with social and technical costs: deciding which uses are legitimate, risking fragmentation, and introducing governance power over transaction content. That path is not neutral, and it carries long-term risks to Bitcoin’s credibility as a rule-of-code system.

Incentives and mining structure: Reducing mining centralization and diversifying block templates can make out-of-band submission less reliable, raising the cost of pushing niche transactions. This is a healthier direction - aligning with Bitcoin’s decentralization goals - but it’s hard, incremental work. And even then, it doesn’t guarantee certain uses disappear; it just changes the reliability and cost of getting them mined.

Between those extremes, tightening local policy feels attractive because it’s easy to do. But ease is not efficacy. What matters is whether your action changes miner behavior or consensus validity. Mempool filters don’t.

> Consensus interventions carry social and technical risks; focusing on decentralizing mining and relay diversity is safer but incremental.
{: .prompt-danger }

## A pragmatic framework for sovereignty-minded users
If you can personally verify the build and signatures, and you accept the governance and privacy tradeoffs, you’re free to run Knots. No one can stop you. Researchers and security engineers comparing Core and Knots can even add value by finding bugs or vulnerabilities that affect both and reporting them responsibly.

For most logical users the pragmatic choice today is Bitcoin Core as it has better:

- Security processes: Distributed maintainership and broad peer review reduce single-actor risk.
- Maintenance cadence: Core ships fixes fast; you’re not waiting on a downstream fork to catch up.
- Ecosystem compatibility: Core’s defaults do not silently block widely used privacy tools like BIP47/PayNym notifications and Whirlpool Tx0.
- Predictability: Your mempool view aligns with the wider network, improving fee estimation and reducing surprises.

> Default recommendation: Bitcoin Core for most users (distributed governance, faster fixes, privacy tool compatibility, aligned mempool view).
{: .prompt-tip }

If you are tempted by stricter policy for philosophical reasons, consider these safer alternatives before switching clients:

- Tune Core within its supported policy surface, where appropriate
- Support projects that decentralize mining and improve relay diversity instead of pinning hopes on local filters.
- Contribute to or review research that quantifies mempool dynamics, out-of-band pathways, and the actual inclusion patterns of the transactions you dislike.
- Consider not upgrading to versions of Bitcoin Core that may include changes you ''don't agree with''

> Tighter local policy rarely changes on‑chain outcomes. Prefer tuning Core within supported bounds and supporting decentralization efforts.
{: .prompt-info }

Sovereignty is not about choosing the strictest knob - it’s about choosing the path that best preserves your security, privacy, and agency in the world we actually have.