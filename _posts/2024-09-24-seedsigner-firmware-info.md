---
title: SeedSigner "firmware" isn't what you think it is
date: 2024-09-27 12:30:00 +0000
categories: [Blog, Security]
tags: [bitcoin,seedsigner,secuty]     # TAG names should always be lowercase
description: A clear breakdown of why SeedSigner’s “firmware risk” is misunderstood, and how the real vulnerability lies in the SD card, not the device itself.
# comments: false
author: keith_mukai
---

On [Rabbit Hole Recap #320](https://youtu.be/yD-3Z9zl_QU?t=2277), ODELL said:

> _"If you are using a SeedSigner in single sig you have to make sure that that device never leaves your sight. If someone gets access to it, they can put malicious firmware on it. [...] And this idea that you're going to share it among communities in single sig is batshit crazy. That is not a good trust model."_

This was a few weeks back. Things have gotten spicier since.

My goal here is to LOWER the temperature.

Hopefully my non-toxic <em>bona fides</em> are clear over my 5 yrs in the bitcoin FOSS space. I'm not here to attack anyone or stir up any hornets' nests. Just going to provide clarifications and explanations that I think ODELL is missing.


## Firmware vs Operating System vs Program
Let's say you're building a Windows or Linux machine from scratch. You've got a motherboard, cpu, RAM. And a brand new blank 2TB disk (nvme, ssd, whatever).

When you first power this new machine on, there's obviously no operating system installed yet.

And yet the computer still does something. It shows you an initial boot screen. Hitting a certain key even opens up an interactive menu.

![BIOS setup screen example](https://i.nostr.build/2OR1oik0wS8YMJVo.webp){: width="250" }

![BIOS menu example](https://i.nostr.build/E5nWuzz5H3v5jH0f.png){: width="250" }

BUT WHERE IS THAT COMING FROM?! YOUR DRIVE IS STILL BLANK!!!

The motherboard itself has a BIOS stored onboard. BIOS is just another word for firmware, basic instructions that tell the hardware what to do. It sets the foundation.

And often the first thing you need to do is update the BIOS. You download a file, copy it over via usb stick, and tell the BIOS to overwrite itself with this new version. And again, you're storing these changes <em>on the motherboard itself</em>.

Operating system can then be installed on your 2 TB drive. The OS runs on top of all the hardware services provided by the BIOS.

Then programs (e.g. SeedSigner's python code) run on top of both of those foundational layers.


## Broad overview of risks
Our stack now has four layers, from top to bottom:

* Individual programs
* Operating system
* BIOS / firmware
* Hardware

Roughly speaking, possible attacks are easier to pull off at the top of the stack and get increasingly harder -- nearly to the point of absurdity -- as you progress down into the deeper layers.

_(caveat: this is a monstrously incomplete discussion of an impossibly deep subject)_

Top layer attacks are trivial, especially for SeedSigner. Our code is completely accessible to the world and the python is very easy to read and modify. If you're tricked into running evil python, game over.

OS layer attacks already get much trickier. Here we assume you're running legit SeedSigner code on top of it, but perhaps some OS exploit lets an attacker override or overwrite the SeedSigner code or somehow manipulate data in memory or mess with calculations. But since a SeedSigner release image includes our python code and our Linux OS, simply modding the python in an evil release is by far the easier option.

Firmware layer attacks get extremely exotic. Attacks here have to be incredibly clever in order to compromise anything happening in legit upper layers. And usually there's only a tiny amount of onboard BIOS space available to begin with.

And for the Hardware layer, see Addendum at the end.


## Key difference for retail hardware wallets
Retail hardware wallets do NOT use this stack. They are NOT mini Linux computers. They run on microcontrollers that are programmed specifically to do one and only one thing: be a hardware wallet. They have a single internal program that's physically stored on their internal board that defines their entire existence.

Their stack:

* Internal program + custom hardware drivers/etc
* Hardware

The top layer is combined in this case because "a firmware update" can change any and all of it at once. The risk here is that you're updating your device's one and only onboard program. And obviously if that new program is evil, your device is now pwned (though most retail devices have hardware-level protections that will reject evil updates).


## Important facts about the Raspberry Pi Zero
The popular Raspberry Pi devices that most people are familiar with are full Linux computers. Even the tiny $10 Pi Zero that SeedSigner runs on is a full Linux computer.

<img src="https://i.nostr.build/K1MUF8boPLDS2LxN.jpg" width="250" alt="Raspberry Pi Zero board">

And so, just like the above, they have their version of a BIOS. Raspis have firmware. You'll see it mostly referred to as a "bootloader". I'll be using the terms interchangeably.

The full-size Pi 4B and Pi 5 that people run bitcoin nodes on (waste of money; do better!) have an onboard [EEPROM](https://en.wikipedia.org/wiki/EEPROM) that stores their bootloader. The second "E" in "EEPROM" stands for "Erasable"; you can update the Pi 4B and 5 firmware, just like you can your desktop machine's BIOS.

<img src="https://i.nostr.build/L2wkixoYxxE2MdB9.jpg" width="250" alt="Raspberry Pi firmware EEPROM example">


### BUT THE PI ZERO IS DIFFERENT
THE PI ZERO HAS NO EEPROM.

THE PI ZERO'S ONBOARD FIRMWARE CANNOT BE CHANGED!!

(same for the Zero W and Zero 2W variants)

<img src="https://i.nostr.build/Wgbxwlc4wkZQ65wB.png" width="600" alt="Pi Zero boot ROM documentation screenshot 1">

<img src="https://i.nostr.build/RIPVVPwWf9nGScjV.png" width="600" alt="Pi Zero boot ROM documentation screenshot 2">

see: [link1](https://forums.raspberrypi.com/viewtopic.php?t=249725#p1524671), [link2](https://forums.raspberrypi.com/viewtopic.php?t=370400#p2219122)

The [official bootloader update instructions](https://www.raspberrypi.com/documentation/computers/raspberry-pi.html#raspberry-pi-boot-eeprom) explicitly call out the Pi 4B and 5 models and go on to state that "All other models of Raspberry Pi computer use the bootcode.bin file located in the boot filesystem."

Okay, so where's the "boot filesystem"? That's on the user's SD card.

So the Pi Zero -- the board that SeedSigner runs on -- only has a basic firmware ("permanent boot ROM") that doesn't do much more than say: "okay, initial power up done, now go get all the real boot instructions (the rest of the "firmware", so to speak) from the SD card".

That first stage bootloader that lives on the board cannot be updated.


## So let's look at the SD card
Unlike a normal computer, the Pi Zero has no disk drive. So the whole OS and any programs you want to run all have to load from the SD card.

Write Raspberry Pi OS (a variant of Linux) to an SD card, pop it in, you're running Raspberry Pi OS. Write debian 12 to a different SD card, swap that SD into your Pi Zero, cycle the power, now you're running debian 12.

For a Pi Zero, the SD card's operation is exactly analogous to running Tails from a usb stick on a normal computer: It boots up whatever is inserted at the time, makes no changes to the hardware itself (Tails leaves it alone by choice; Pi Zero hardware can't be altered, period), and when you power down <em>it leaves no trace behind</em>, as if it never ran at all.


## Now we can dig in: SeedSigner "firmware"
_"If someone gets access to [your SeedSigner / Pi Zero], they can put malicious firmware on it."_

I think ODELL is mistakenly applying a retail device's notion of "firmware" to how the Pi Zero works. Totally understandable, but wrong.

Malicious "firmware" attacking a retail hardware wallet would be changing the device's one and only internal program. Changing its core essence, potentially all of its behavior. Bad, bad, bad.

Malicious "firmware" attacking SeedSigner's Linux stack would exist two layers below our python code (both of which are over on the SD card), and would have to overcome all the challenges that an attack down at that low level faces.

But even more significant, as outlined above, no one can even change the "firmware" -- the first-stage bootloader -- on the Pi Zero hardware. This specific attack he's warning about is NOT viable.

In fact, the Pi Zero has NO ONBOARD STORAGE of any kind where you could stuff malicious code.

I suspect that he was not aware of this hardware limitation.

🚨 THIS IS A CRUCIAL MISUNDERSTANDING! 🚨


## Risks remain... but only on the SD card
Uncontroversially true: You ARE at risk if an attacker:
* Secretly modifies your SD card.
* Or tricks you into writing their malicious code to your SD card (e.g. downloading an "evil" release).

So the most likely risk if you share your SeedSigner is:
* You lend your SeedSigner AND your SD card to your dumb cousin Larry.
* Larry sees there's a "new" SeedSigner release and (foolishly) flashes it to the SD card.
* You get the SeedSigner AND the now-evil SD card back.
* You (foolishly) use it as-is.

Or Larry is actually your really-smart-but-evil cousin and he intentionally turns your SD card evil. Same diff.


## So is it "batshit crazy"?
> _"And this idea that you're going to share [a SeedSigner] among communities in single sig is batshit crazy."_

We now know that the only practical<sup>*</sup> attack involves the SD card.

_<sup>*</sup>(more sophisticated hardware attacks discussed in the addendum below)_

So:
* Ideally DON'T share a SeedSigner if you can avoid it.
* But if you do, each user should only use their own SD card OR freshly reflash one.
  * Obv you better verify the release image before flashing.

It's stupid-fast to flash a SeedSigner release image; our most recent [v0.8.0 release](https://github.com/SeedSigner/seedsigner/releases/tag/0.8.0) is just 26 MB. The whole process takes just a few seconds. Also note that you're not just copying a file over, you're literally reformatting and repartitioning the SD card as a drive.

So ODELL's _"you have to make sure that that device never leaves your sight"_ admonishment isn't accurate for the Pi Zero hardware and is easily mitigated even for the SD card.

Passing around empty hardware (no SD card) amongst trusted friends and relatives is NOT "batshit crazy." Of course it's not ideal. But it's not the omg-you-guys-are-so-f'n-stupid-what-the-fuck-is-wrong-with-you recklessness that ODELL portrays it as.

I think he just misunderstands how a Pi Zero works.


## Bad premises lead to bad conclusions
> _"There are multiple use cases that people are recommending for SeedSigner and I think they're doing it in bad faith. [...] It's incredibly disappointing to talk to the lead of that project [SeedSigner the man] privately -- who I consider a friend -- and he tells you one thing and then he goes out in the internet and is just bullshitting."_

Look, I don't know the full context here, but I can totally understand a sequence of thought that goes:

<em>Given the risk of `X`, it would be "batshit" to recommend `A`, `B`, or `C`, therefore anyone who does so is horrible.</em>

But if that premise `X` is what we've been discussing here, IT DOES NOT HOLD.

How much is that one misunderstanding sinking everyone's ship here?


## What HAS SeedSigner (the man) said about device sharing?
<img src="https://i.nostr.build/LaEklHSqE1J9GgF0.png" width="600" alt="SeedSigner device sharing guidance screenshot">

(I'm sure there are even earlier examples of him expressing this nuance, but saying this 20mo ago establishes a deep enough track record)

To be clear, when he says "trusted third parties" / "Trust between share-ers" he's talking about family, about close friends that you trust with your life. He explicitly rules out a "publicly-shared" SeedSigner amongst strangers. ODELL incorrectly frames the context as "share it among communities".

SeedSigner (the man) recently reminded me that in his former profession doing digital forensics, he'd often be called on the stand to testify about his investigations. So he cultivated a very consistent and very careful way of discussing tech, tradeoffs, what you can or can't be sure of, etc. One careless statement lacking nuance could jeopardize a case.

That precision has always stood out to me in every conversation I've had with him and in what I've seen him share out to the world.

<em>Huge non sequitur -- but relevant!: I love listening to Allen Farrington interviews because every sentence has like 5 parenthetical asides and clarifications mid-sentence. He speaks like I write; I can't bear to let a broad statement stand without at least some parenthetical additional nuance. So when I say that I've always noticed SeedSigner (the man)'s precision, I mean it.</em>

Now is it possible that SeedSigner has said something sloppily? Yeah, of course. But given the above, I definitely think he's more precise and more consistent and more careful than most.

And has he had bad takes or unfair or overly harsh critiques on OTHER topics? Yeah, of course (I've had some brutally honest discussions with him in private on occasion and have uttered some "I strongly dislike when you..." pushback in public channels when I thought it was needed).

But on THIS topic -- the real world risks of sharing a SeedSigner with people in your inner circle -- I think he's been on-point and totally clear.


## ODELL IS right about...
> _"[SeedSigner] requires much more personal responsibility on the user."_

100%. I think we're explicit about that. And I don't think that's a perfect fit for everyone.

> _"You also have to make sure that every time you load firmware that you actually verify that that is not malicious firmware and that it hasn't been modified."_

Obviously we have to lightly edit ODELL's sentiment given this whole discussion above.

_"...every time you FLASH A RELEASE IMAGE TO AN SD CARD you actually have to verify..."_ Yes, 100%.

_"...every time you INSERT AN SD CARD INTO YOUR SEEDSIGNER..."_ Here the risks and mitigations depend on your personal threat model. Did you let cousin Larry anywhere near your SD card? Is it possible an evil maid has tampered with your SD card while it was sitting in storage the past month? etc, etc.


## Wrap it up
What have I missed?

What did I get wrong?

What other "bad faith" or "bullshitting" talking points has SeedSigner (the man) or others put out there?

---

# Addendum
This is all way too f'n long already, but for the sake of completeness:

## Direct hardware attacks
So attacking a Pi Zero with malicious firmware just isn't a real attack vector. And attacks on your SD card are trivially mitigated by verifying your downloads and reflashing the SD as often as needed.

But to be as fair as possible, there are still some hardware-level attack vectors that sharing a device potentially opens you up to:
* Maliciously modified hardware (extra chips, transmitters, etc).
* Wholesale replacement; legit Pi Zero out, evil custom clone hardware in.
* Tricksy SD card that you can reflash but it secretly preserves and runs its own evil partition anyway.
* Others? (I'm not a hardware security expert, y'all)

To me this starts to get to more NSA-level attacks, Russian spies with a nation-state's resources at their disposal. Possible? Yes, of course. Probable? Depends on who you are and how much corn you're securing. But most likely: you aren't a juicy enough target for this level of effort.

And by the way, the main context for the device sharing convo is in places like El Salvador where it just isn't viable for everyone to buy a $150 retail hardware wallet for themselves ([avg income is $350/mo](https://www.timecamp.com/average-salary/el-salvador/)). We're talking about a family investing $50 to build a SeedSigner and maybe sharing it within their close circle. And obviously the stack that they'll be securing will be pretty modest.

Criticize me as being overly dismissive here, but I just don't see any of these more sophisticated hardware attacks as being in any way likely to happen in that context.

If we shift the context to the US, there's just less need and less likelihood to share a SeedSigner in the first place. But even still, most of us plebs aren't terribly worthwhile targets anyway. Attackers are much more likely to create a fake SeedSigner download link to try to get you to pwn yourself (low effort, potentially large number of victims) than to focus their efforts on just infiltrating your specific device.


## If you are worried about sophisticated hardware mods
Inspect your Pi Zero board, compare it to all the photos that are easy to find online. Seal your enclosure in some kind of tamper-evident holographic tape. Or have your Aunt Betty order a brand new Pi Zero from [Adafruit](https://www.adafruit.com/product/2885).

It's the NSA tho; plebs just have to assume they're invincible. 🤷‍♂️


---

## Edit notes
* 2024-09-25: Wrote "ODELL" with proper all-caps!
* 2024-09-25: Removed discussion about hardware attacks that come directly from the factory; a possible threat, yes, but has nothing to do with risks related to sharing a device.
* 2024-09-26: Added [Broad overview of risks](#broad-overview-of-risks), [Key difference for retail hardware wallets](#key-difference-for-retail-hardware-wallets) sections; updated [discussion](#now-we-can-dig-in-seedsigner-firmware) accordingly.
* 2024-09-27: Fixed Adafruit link.
