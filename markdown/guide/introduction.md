---
title: "Introduction"
description: "What AccessBay is, who it is for, and what works in the current pre-release."
---
AccessBay is a WordPress plugin for selling plugins, themes, software and other digital goods from your own site. It
is built by WPAnchorBay and released under the GPL (version 3 or later).

## What AccessBay will do

The first release is planned to cover:

- **Licensing:** licence keys, activations and seats for the software you sell.
- **Update delivery:** your customers' sites receive updates for the software they bought.
- **Protected downloads:** digital goods that only paying customers can download.

At first, orders come from WooCommerce: a paid WooCommerce order creates the matching AccessBay licence and download.
A checkout built into AccessBay, without WooCommerce, is planned for a later release. An optional AccessBay Pro plugin
will add further features on top of the free plugin.

## What works today

AccessBay is in development, and this is a pre-release. It does **not** yet sell products, issue licences or deliver
downloads.

What is in place is the foundation those features stand on:

- Activation checks that refuse to start on a server that can't run AccessBay safely
  ([Requirements](/guide/requirements/)).
- A site secret that protects stored keys and credentials ([The secret key](/guide/secret-key/)).
- Health checks in Site Health, in WP-CLI and over REST for an external monitor
  ([Health checks](/guide/health-checks/)).
- Background jobs and an email queue ([Email setup](/guide/email/)).
- The REST API foundation: authentication, errors and rate limits
  ([REST conventions](/developers/rest-conventions/)).

Everything else on these pages that is not built yet says so.
