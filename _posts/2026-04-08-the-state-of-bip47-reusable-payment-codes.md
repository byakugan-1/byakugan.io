---
title: BIP47, Silent Payments, and the Future of Private Receiving
date: 2026-04-08 16:00:00 +0000
categories: [Blog, Privacy]
tags: [bitcoin,privacy,bip47,paynyms,auth47,silentpayments,ashigaru,sparrow]
description: A broad look at Bitcoin privacy, BIP47 and Silent Payments
# comments: false
pin: true
author: vibrant
image: /assets/img/bip47-vs-sp/bip47.jpg
---

Bitcoin privacy is often discussed in a way that feels almost designed to be unhelpful. Some people speak as though everything is already private enough if you simply avoid doing anything too embarrassing. Others speak as though the whole thing is a lost cause, a public panopticon in which every attempt at improving your position is just theater.

Neither camp is especially serious.

Bitcoin gives you room to behave intelligently, but it does not protect you from your own habits. If you leave obvious patterns lying around, the chain will preserve them for as long as anyone cares to look. If you make an effort to reduce those patterns, you can improve things meaningfully. Not perfectly. Not magically. Just meaningfully, which is often enough to matter.

That is why privacy on Bitcoin so often begins with something much less glamorous than people expect.

It begins with how you receive.

## Why privacy on Bitcoin starts with receiving properly

Bitcoin is often described as pseudonymous. That is true in the most technical sense and misleading in the way most people hear it.

Your legal name is not stamped into a transaction, but that does not mean your activity is naturally obscure. Addresses are public. Transaction histories are public. Timing is public. Amounts are public. And once a pattern hardens enough, it becomes easier than many users seem to realize to connect the dots between a wallet, a payment habit, a donation page, a business, a public post, a KYC withdrawal, or a recurring customer relationship.

This is why address reuse is such a stupid mistake.

A reused address can reveal:

- how many times you have been paid;
- roughly when payments arrived;
- whether your activity is growing or drying up;
- whether multiple transactions likely belong to the same person or service;
- whether someone can start stitching your public and private life together using nothing more exotic than a block explorer and a little patience.

People sometimes talk about this as though it were just poor wallet hygiene, like forgetting to wipe a fingerprint off a glass. It is worse than that. It is a structural privacy failure caused by convenience.

And convenience is exactly why the problem persists.

People do not keep reusing addresses because they are malicious or lazy in some grand moral sense. They do it because static identifiers are easy. They do it because asking for a fresh address every single time is clunky. They do it because human beings tend to choose the smoother path, especially when the downside is abstract and delayed.

So any worthwhile solution has to account for that. It cannot simply demand perfect discipline forever. It has to reduce the chance of the mistake happening in the first place.

## The broad solutions to address reuse

Once you accept that a static address is a liability, the next question is obvious enough: what replaces it?

The oldest answer is manual rotation. Generate a fresh address each time, hand it out, and make sure it is used once. This works. It is also tedious, easy to mishandle, and not especially elegant if you are trying to receive repeatedly from the same people or from the public.

The more interesting category is the one that tries to preserve the convenience people want while avoiding the surveillance beacon they accidentally create when they keep posting one public address.

That is where the real discussion now lives.

Today, the two most obvious approaches are:

- BIP47 reusable payment codes, most often encountered through PayNyms;
- Silent Payments.

They are both trying to solve the same broad human problem. They simply make different trade-offs on the way there.

## BIP47 reusable payment codes

BIP47 is one of those Bitcoin ideas that sounds a little more complicated than it really is.

At a high level, instead of handing out a new address each time, you share one reusable payment code. After a relationship is established (via a notification transaction), a sender can derive fresh addresses for future payments without coming back and asking you for new receiving information over and over again.

The practical result is simple enough:

- you avoid repeated address reuse;
- you reduce manual back-and-forth;
- you keep a stable public payment identity without turning that identity itself into one permanent on-chain destination.

That last point is the important one.

A lot of people talk about BIP47 as though it were just a convenience trick for better address management. It is more than that. It is a separation between the thing you expose publicly and the thing that actually shows up on chain when people pay you.

That is a much more meaningful privacy improvement than the phrase "reusable payment code" might initially suggest.

## PayNyms: the human layer on top

In practice, most people do not meet BIP47 first as a raw BIP. They meet it through PayNyms.

That matters.

A raw payment code is useful, but it is still an ugly technical object. A PayNym gives that object a more human shape. It makes identity, recognition, and contact relationships easier to work with. And once that layer exists, the whole thing starts to feel less like a cryptographic curiosity and more like something people can actually live with.

This is one of the reasons BIP47 has held on better than some people expected.

Privacy tools often fail at the border between the protocol and the person. The mathematics can be excellent, but if the interface is brittle, the social layer is absent, and the user has no clear sense of who is who, the tool tends to remain niche no matter how technically sound it is.

PayNyms soften that border.

They make BIP47 more legible. They make repeated relationships easier to preserve without collapsing into address reuse. They create an identity layer that can support more than just receiving. And once you have that, the protocol starts growing outward instead of sitting there as a lonely feature.

## BIP47 explained a little more plainly

For a beginner, the easiest way to think about BIP47 is as a reusable payment handshake.

You share one reusable identifier. A sender uses it to establish a relationship with you. After that, future payments can arrive to fresh addresses without the old ritual of constantly requesting a new one.

The nice part is obvious enough. You keep the convenience of a stable point of contact without collapsing all your incoming activity into one visible address.

The trade-offs are worth naming directly too.

In the standard model, the relationship is established through a notification transaction. That is the piece critics usually point at first. Fair enough. It is an extra step. It is an extra transaction. It is additional structure.

But BIP47 does not technically need to be understood only through that older notification flow forever. In the broader BIP47 world, coordination can also happen through Soroban-based communication, as seen in Cahoots workflows. Soroban has been introduced as the Soroban peer-to-peer network in Samourai Dojo v1.27.0, points in a direction where more of the useful coordination can happen through decentralised communications rather than always leaning on the classic notification model alone. That does not mean the notification transaction disappears from every context today, but it does mean the BIP47 story is no longer frozen in its earliest form.

And the notification transaction has never been a purely negative thing anyway.

Yes, it is less efficient than pretending no setup is needed. But it also means the sender has to deliberately establish the relationship. It creates a boundary. It can be seen as a drawback in efficiency terms, or as a modest advantage in receiver terms because you are less exposed to the kind of lazy public-address model that invites unwanted payment behavior, dusting included. It also avoids pushing recipients toward universal reliance on Taproot outputs, which still do not look like the ordinary spending pattern of serious privacy-minded Bitcoin users today.

That Taproot point deserves to be said more plainly. Silent Payments route you into receiving and later spending Taproot outputs. In some circles that gets presented as automatically modern and therefore automatically better. I do not think that follows at all. Taproot is still much less commonly used than older output types, and if you are serious about spending privately on Bitcoin, voluntarily forcing yourself into a less common spending profile is not obviously a win. Sometimes the shiny thing is just the shiny thing.

There is another honest point about bip47 too: once the relationship exists, the recipient can infer that repeated future payments over that channel are coming from the same sender. That is real. It is not fatal, but it is one of the trade-offs.

So BIP47 is not magic. It is a compromise. A fairly good one.

## Silent Payments

Silent Payments come from a different instinct.

The appeal is easy to understand. Keep the convenience of a reusable public receiving identifier, but avoid the dedicated BIP47-style notification transaction. Share one identifier, let the sender do the derivation work, and receive to fresh outputs without first opening a relationship in the older BIP47 sense.

On paper, that has obvious charm.

It is cleaner. It is leaner. It feels more invisible. It sounds like the kind of thing Bitcoin people naturally like to describe as elegant.

And to be fair, it is elegant.

The public-facing material around Silent Payments leans into exactly those strengths: privacy-preserving static addresses, no special server just to make the basic receiving flow work, and a smoother public story around reusable receiving.

That is a respectable case.

## Silent Payments explained a little more plainly

If BIP47 feels like establishing a reusable relationship and then using it over time, Silent Payments feel more like publishing a reusable receiving marker that lets the sender derive a fresh destination each time without first opening the relationship through a distinct notification step.

For an ordinary user, the pitch is simple:

- one public receiving identifier;
- different outputs on chain;
- no dedicated BIP47-style setup transaction first;
- a very clean conceptual story.

That is why so many people like the idea.

But Bitcoin privacy tools are never judged only by how elegant the first paragraph sounds. They are judged by wallet support, scanning burden, recovery behavior, infrastructure maturity, and whether they can be used in a sovereign way outside carefully curated demos.

That is where the comparison gets more serious.

## BIP47 and Silent Payments are really a discussion of trade-offs

I do not think this needs to become a religious war.

It is good that Bitcoin has multiple attempts at solving reusable private receiving. Different trade-offs are healthy. Different ideas getting tested is healthy. I am not presenting myself here as the final authority descending from the mountaintop with sacred protocol tablets. I am just interested in the subject and having a bit of fun with it.

But all the same, the present picture does not look especially even to me.

Right now, BIP47 looks broader, more stable, and more practical between real wallets.

## Where BIP47 feels stronger today

The first point is simply support.

BIP47 is fully supported by Samourai Wallet, Ashigaru Wallet, and Stack Wallet, and supported in Sparrow Wallet as well. This is not some dusty standard kept alive by one stubborn implementation and a few loyal forum posts. It is actually used.

And it is not used only by wallet hobbyists talking among themselves.

Companies and services such as The Bitcoin Company, [mynymbox.io](https://mynymbox.io/), and Lincoin are already living in this world. People use PayNyms for donations in the wild. A good example is [GrapheneOS donations](https://grapheneos.org/donate#bitcoin), where BIP47 is present and Silent Payments are nowhere to be seen.

That matters more than elegance contests do.

A privacy standard is stronger when real people are already using it publicly, when businesses already recognize it, and when support exists across multiple serious wallets rather than being concentrated in one place.

The second point is that BIP47 has grown into more than just a receiving method.

It enables PayNyms.
It enables an identity layer.
It enables Auth47.
It enables collaborative possibilities through Soroban and Cahoots.

That breadth is not cosmetic. It is the difference between a protocol and an ecosystem. - This is why the arrival of [paymentcode.io](https://paymentcode.io/) feels well-timed. The protocol has needed a calmer, more public-facing home for a while now.

The third point is sovereignty.

This is where the gap becomes harder to ignore.

With BIP47, the surrounding models for scanning, wallet coordination, recovery, and contact relationships are better understood and more mature. They are not perfect, but they exist in forms that serious self-hosting and privacy-minded users can reason about.

With Silent Payments, the scanning problem is much uglier in practice. If you want to detect your own incoming Silent Payments in a sovereign way, you need infrastructure that can actually index and surface what your wallet needs to know. And that is the rub: you are not just looking up a familiar address history and moving on with your day. You are dealing with a model where the wallet has to identify which outputs on chain belong to you without the support of the kind of mainstream, settled, self-hostable indexing stack that privacy-minded users usually want before they trust a workflow long term.

Right now there is no mainstream, self-hostable, non-experimental indexing stack that ordinary users can confidently point to and say: yes, this is the normal way I privately and locally compute my Silent Payments receives. That is a major weakness, not a footnote.

It means the clean story at the surface is carrying a much heavier and much less mature burden underneath.

And the wallet situation does not help.

Today the practical answer to "what wallet do I use for both sending and receiving Silent Payments?" is basically [Cake Wallet](https://cakewallet.com/). That is a massively bloated multi-coin wallet. Even if one wants to be generous about its willingness to ship features, the broader point remains: one wallet doing most of the practical work for a standard is not ecosystem strength. It is ecosystem fragility.

## Where Silent Payments make a fair case for themselves

None of that means Silent Payments are pointless.

They still have a real argument in their favor.

The strongest point is the obvious one: in the standard receiving flow, there is no BIP47-style notification transaction.

That means:

- one less explicit setup step;
- one less dedicated transaction before useful receiving begins;
- a cleaner conceptual model for people who place a premium on efficiency and invisibility.

That is not trivial.

There is also a genuine elegance to the design. People are not imagining that part. If you are the sort of person who instinctively prefers less ceremony and fewer moving parts in the first visible layer, Silent Payments make immediate emotional sense.

And I do think it is healthy for Bitcoin that people are exploring that direction seriously.

But elegance alone does not settle the question.

## The BIP47 trade-offs are real too

If I am going to come down more confidently on one side, I should be just as willing to name its imperfections.

The notification transaction is still a trade-off.

Depending on how you look at it, it is either needless friction or useful structure. In truth it is a bit of both.

And once a BIP47 relationship is established, repeated payments through that relationship can be understood by the recipient as coming from the same sender. If you want less sender continuity visible to the receiver, this is a real limitation.

There is also the historical issue that BIP47’s social and directory layers were not as decentralised as many people would have liked.

That criticism was always fair.

But unlike a lot of criticisms, that one now has interesting answers emerging around it.

## Why the direction of BIP47 now matters more than the old caricature

One of the laziest ways to talk about BIP47 is to pretend it is permanently trapped in its earliest form.

It is not.

Soroban matters here. More specifically, decentralised Soroban matters here. The fact that Samourai Dojo v1.27.0 introduced the Soroban peer-to-peer network points toward a version of the BIP47 world that leans less on brittle central coordination and more on direct encrypted communication between peers. In Cahoots contexts, BIP47-style identity and coordination are already being used in a way that shows the protocol family does not have to remain locked forever to the old mental model of "notification transaction or nothing." 

That is important.

And then there is [BIP47DB](https://bip47db.github.io/) - an open protocol for inscribing BIP47 reusable payment codes onto the Bitcoin blockchain using Ordinals inscriptions with compressed binary encoding. The protocol creates a decentralised, censorship-resistant, and publicly verifiable directory of payment codes that eliminates single points of failure in the PayNym ecosystem.

This is not the main focus of this blog, but it is certainly not an afterthought either. If BIP47DB comes to fruition, it would give the payment-code ecosystem something it has long needed: a much stronger redundancy, once inscribed, payment code data cannot be seized, altered, or deleted by any authority

Put those two directions together — a more decentralised Soroban peer-to-peer network on one side, and something like [BIP47DB](https://bip47db.github.io/) on the other — and suddenly BIP47 looks less like an older protocol carrying baggage and more like a protocol family that may be on the verge of becoming quite difficult to knock over.

That is not a weak future. That is a strong one.

## A word on the future

Silent Payments may improve from here. [Frigate](https://github.com/sparrowwallet/frigate), Craig Raw’s experimental Electrum server work, is one example of people trying to address the local indexing and scanning reality around Silent Payments. That is worthwhile work, and if that world becomes much easier to run privately and locally, the comparison will become more competitive.

But today is not the future. Today is today.

And today BIP47 has the stronger position.

It has broader wallet support.
It has more real-world usage.
It has a social and identity layer people actually use.
It has better understood sovereign operating models.
It has clearer paths toward more decentralised coordination.
And it has an exciting infrastructure tailwind in [BIP47DB](https://bip47db.github.io/).

Silent Payments are interesting.

BIP47 is alive.

That is a much bigger compliment.

## Final thoughts

If the question is which reusable receiving approach looks better in abstract protocol discussions, people will keep arguing for a long time.

If the question is which one looks more mature, more usable, more sovereign, and more grounded in the actual wallet ecosystem right now, I do not think the answer is especially close.

It is BIP47.

Not because it is flawless. Not because no one can design anything better in theory. But because it already exists as something people can use across real wallets, real companies, real donation flows, and real identity relationships without waiting for some imagined future to arrive and rescue it.

That matters.

Bitcoin privacy has enough speculative castles already. I have more respect for the thing people are actually using in the wild.

And if BIP47 keeps moving toward more decentralised coordination through Soroban, while ideas like [BIP47DB](https://bip47db.github.io/) keep pushing the redundancy and resilience story forward, then the case for it only gets stronger.

I will leave you with this:

> “What SilentPayments wallet do you use?”

## References

- [paymentcode.io](https://paymentcode.io/)
- [BIP47 Specification](https://github.com/bitcoin/bips/blob/master/bip-0047.mediawiki)
- [Silent Payments](https://silentpayments.xyz/)
- [Silent Payments BIP-352](https://bips.dev/352/)
- [BIP47DB](https://bip47db.github.io/)
- [GrapheneOS donations](https://grapheneos.org/donate#bitcoin)
- [Cake Wallet](https://cakewallet.com/)
- [Craig Raw’s Frigate repository](https://github.com/sparrowwallet/frigate)
- [Ashigaru announcement: a new PayNym directory](https://ashigaru.rs/news/announcement-paynyms/)
- [Ashigaru proof of ownership](https://ashigaru.rs/proof-of-ownership/)
- [Mynymbox](https://mynymbox.io/)
- [The Bitcoin Company](https://thebitcoincompany.com/)
- [Lincoin](https://lincoin.com/)
