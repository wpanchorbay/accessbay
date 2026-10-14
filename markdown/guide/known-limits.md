---
title: "Known limits"
description: "What this release of AccessBay leaves out, and how updating from one release to the next is meant to work."
---
AccessBay is a pre-release. This page lists what the current release leaves out, so you can decide whether it fits your store. The [introduction](/guide/introduction/) says what works today.

## What this release leaves out

- **Native checkout.** Orders come from WooCommerce: a paid WooCommerce order creates the matching AccessBay licence and download. A checkout built into AccessBay, which would let a store sell without WooCommerce, is planned for a later release. Until then you need WooCommerce.
- **Subscriptions.** Invoice and card renewals collected by AccessBay are planned as a Pro feature in a later release. Renewing a licence by paying again for it, through WooCommerce, is planned earlier.
- **A WordPress.org listing.** We plan to list the free plugin on WordPress.org. It is not listed yet, so you install AccessBay from a zip.

## Setting up products from the command line

You normally manage products and prices on their admin screens ([Products](/guide/products/)). WP-CLI can create them too, which is handy for scripts and for seeding a test site. Each command prints a table or, with `--format=json`, JSON; `--format=ids` prints only the new id, for scripts. A refused input exits with status 1 and prints the reason code, such as `duplicate_slug`. All of them work in every mode, live included.

| Command | What it does |
| --- | --- |
| `wp accessbay products create --type=plugin --slug=<slug> --main-file=<file> --update-uri-host=<host> [--name=<name>] [--status=draft\|active]` | Creates a product. `--type` is `plugin`, `software` or `digital`; `plugin` needs `--main-file` and `--update-uri-host`. The status defaults to `draft`. |
| `wp accessbay products list` | Lists products, up to 200. |
| `wp accessbay products get <id>` | Shows one product. |
| `wp accessbay prices add --product=<id> --amount=<minor units> --currency=<ISO code> --seats=<n> --days=<n> [--name=<name>]` | Adds a one-time price whose licence allows `<n>` seats for `<n>` days. |
| `wp accessbay prices list --product=<id>` | Lists a product's prices. |
| `wp accessbay customers create-guest --email=<address>` | Creates a guest customer. Running it again with the same address, in any letter case, returns the same customer. |

`wp accessbay licenses issue --customer=<id> --price=<id>` then issues a licence for what you created.

## Updating from one release to the next

Moving from one release to the next is meant to keep every licence, activation and release you have. Refusing a downgrade, with a message that names both versions and says how to roll back, is planned too. Neither has been tested yet.

Until they are:

- Back up your database before you update.
- Keep the zip of the release you are replacing, so that you can go back to it.
