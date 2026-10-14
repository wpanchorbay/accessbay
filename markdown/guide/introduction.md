---
title: "Introduction"
description: "What AccessBay is, who it is for, what it does today, and a tour of its screens."
---
AccessBay is a WordPress plugin for selling plugins, themes, software and other digital goods from your own site. It
is built by WPAnchorBay and released under the GPL (version 3 or later).

## What AccessBay does

- **Licensing:** licence keys, activations and seats for the software you sell.
- **Update delivery:** your customers' sites receive updates for the software they bought.
- **Protected downloads:** digital goods that only paying customers can download.

At first, orders come from WooCommerce: a paid WooCommerce order creates the matching AccessBay licence and download.
A checkout built into AccessBay, without WooCommerce, is planned for a later release. An optional AccessBay Pro plugin
will add further features on top of the free plugin. [Known limits](/guide/known-limits/) lists what the current
release leaves out.

## A tour of the screens

Open AccessBay in the WordPress admin menu. On a laptop or a wider screen, the sidebar on the left holds everything
you need to move around. There is no bar across the top.

From top to bottom, the sidebar has:

- The AccessBay logo. A "Test mode" tag sits next to it while the plugin is in test mode.
- A **Search** box. Select it, or press `/`, to search licences, customers, orders, products and settings
  ([Finding things](/guide/finding-things/)).
- The screens, with the Dashboard first and then four groups: **Sales** (Products, Orders, Customers, Woo mapping),
  **Licensing** (Licences, Activations, API keys), **Email** (Email providers, Email settings, Email log) and
  **System** (Health, API test console, Settings). Add-ons, such as AccessBay Pro, can add items of their own, under
  **Add-ons** if they name no group.
- A footer with a link to the documentation (**Docs**) and the theme menu, where you choose a light or dark look.

On a tablet or phone, a slim bar at the top holds the logo and a menu button. The menu shows the same screens, and
also search, the theme choice, settings and docs.

Each screen starts with a title line. On a screen that sits inside another, such as one product, the title line shows
the parent first as a dimmed link, as in "Products > Add product". Select the parent to go back. The title line stays
in view when you scroll, just under the WordPress admin bar.

| Screen | What you do there |
|---|---|
| Dashboard | See store counts and the next steps to take. |
| [Products](/guide/products/) | Create and manage what you sell, its prices and its releases. |
| [Orders](/guide/orders/) | See the orders that created licences. |
| Customers | See who bought from you, with their email, company, country and join date. |
| [Licences](/guide/licences/) | Find a licence and act on it. |
| [Activations](/guide/activations/) | See which sites use a licence and deactivate one. |
| API test console | Try the licence calls against test licences. It works in test mode only. |
| Woo mapping | Link each WooCommerce product to an AccessBay product. |
| [Email log](/guide/email/#the-email-log) | See the email AccessBay and your site send. |
| [Settings](/guide/plugin-settings/) | Change how AccessBay behaves, and add email providers ([Email setup](/guide/email/)). |
| [Health](/guide/health-checks/) | See what AccessBay verified about your site, and fix what it found. |

The list screens share one layout, with a search box, filters and saved views. [Finding things](/guide/finding-things/)
explains it once.

## Before you start

- [Requirements](/guide/requirements/): what your server needs.
- [Installation](/guide/installation/): install and activate the plugin.
- [The secret key](/guide/secret-key/): the site secret that protects stored keys and credentials.
- [Health checks](/guide/health-checks/): how AccessBay checks its own setup.
- [Email setup](/guide/email/): how AccessBay sends email.
- [REST conventions](/developers/rest-conventions/): the rules for AccessBay's REST routes.
