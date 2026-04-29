---
title: "Security Policy"
description: "How to responsibly disclose security vulnerabilities in TALER Blockchain — scope, reporting channels, response expectations and safe-harbour principles."
---

TALER Blockchain takes the security of its software, network and users seriously. This page describes how to report a vulnerability and what to expect afterwards.

## How to report

The preferred channel is **email** to <info@taler.tech>. Please include:

- a clear description of the issue and its impact;
- steps to reproduce, ideally with a minimal proof-of-concept;
- the affected component, version or commit hash;
- your name or handle if you wish to be credited.

If you prefer to use Telegram for an initial private contact, message a maintainer on [our channel](https://t.me/talercommunity) and ask to move to email — do **not** post vulnerability details in any public channel.

## In scope

- The reference node implementation ([`abkvme/taler`](https://github.com/abkvme/taler))
- The block explorer ([`abkvme/taler-explorer`](https://github.com/abkvme/taler-explorer))
- This website (`taler.tech`) and any service hosted directly under it (for example, `explorer.taler.tech`)
- Cryptographic and consensus issues that affect mainnet (PoW/PoS, Lyra2Z, Dark Gravity Wave)
- Wallet-encryption, key-derivation and seed-handling code

## Out of scope

- Third-party wallets, exchanges, mining pools and explorers that are not operated by the project, even if linked from this site
- Social-engineering or phishing reports against individual community members
- Denial-of-service that requires sending traffic disproportionate to a normal user
- Reports based solely on automated scanner output without a demonstrable security impact
- Issues already publicly disclosed or already tracked on the public GitHub issue tracker

## What to expect

TALER Blockchain is an open-source, volunteer-driven project. There is **no SLA, no guaranteed response time and no obligation** for anyone to act on a report — neither at this email, on Telegram, in GitHub Discussions, nor anywhere else.

What may happen if a community member picks up your report:

- They contact you to ask follow-up questions.
- They reproduce the issue, propose a fix or mitigation, and coordinate quietly with you on timing before any public write-up.
- The fix lands in a release; the disclosure is described in the [release notes](/en/release-notes/) and the project's GitHub repository.

If no one picks up the report, nothing happens. Please report anyway — public visibility of the report (without exploit details) is one of the things that makes someone pick it up. After a reasonable wait you are free to disclose publicly, ideally without exploit details until users have had time to update.

## Safe harbour

We will not pursue legal action or report to law enforcement against researchers who:

- act in good faith and avoid privacy violations, destruction of data and disruption of service;
- make a reasonable, non-coercive effort to contact us before disclosing publicly;
- give us a fair window to fix the issue before any public write-up;
- comply with applicable law.

We do **not** run a paid bounty programme and we cannot promise CVE credit, a Hall-of-Fame entry, or any other form of recognition — that depends entirely on whether a community member picks up the report and chooses to do so.

## Acknowledgments

If a community member acts on a report and the reporter wants to be named, the reporter may be listed below. There is no guarantee of inclusion.

_No public reports yet._
