---
title: "Features and use cases"
description: "Everything AccessBay does today, grouped by what you do with it, and the kinds of software business it fits."
---
AccessBay turns the WooCommerce store you already have into a licence server for the plugins, themes and software you
sell. This page lists what works in the current release. Anything planned is on the [Roadmap](/roadmap/), not here.

## Features

### Products and releases

- **Products and prices.** Create what you sell as software (a plugin), a digital download or a bundle. Each product
  has prices, and a product stays a draft until you make it active. See [Products](/guide/products/).
- **Releases.** Upload a release, publish it, and pull it back (yank it) if it is bad. The software you sell receives
  a published release as an update.
- **Archiving.** Archiving a product ends new sales. You choose whether customers with a valid licence keep their
  downloads and updates ([Plugin settings](/guide/plugin-settings/)).

### Licensing

- **Licence keys, activations and seats.** A paid order creates a key. The buyer's site activates it, and each licence
  allows a number of sites. See [Licences](/guide/licences/) and [Activations](/guide/activations/).
- **Control from the admin.** Extend a licence by a number of days, add seats, suspend it, revoke it or replace its
  key. Deactivate a site and its seat is free again.
- **Staging sites.** AccessBay can treat a copy of a site as staging, with its own allowance.
- **Test mode.** Keep test orders and licences apart from real ones, and try the licence calls in the API test
  console.

### Orders and checkout through WooCommerce

- **Paid orders create licences.** You map each WooCommerce product to an AccessBay product, and a paid order issues
  the licence on its own.
- **Refunds and disputes reverse access.** A refund on the order ends the licence's access. You record a dispute on
  the order in AccessBay.
- **Orders screen.** See where each order came from, its payment status and whether the buyer received what they
  bought. See [Orders](/guide/orders/).

### Delivery and updates

- **Updates in wp-admin.** Your customers' sites are offered new versions of your plugin or theme in the usual
  WordPress way, from your own site.
- **Signed and short-lived.** Update information is signed, and download links expire.
- **Client SDK (beta).** Add the SDK to your plugin or theme to activate a licence and ask your site for updates. It
  comes as a separate zip with each release, and gives you a licence screen, WP-CLI commands and a Site Health test.
  See [SDK](/developers/sdk/).

### Customers

- **Key on the thank-you page and in email.** The buyer sees the licence key right after paying and gets it by email.
- **Customer portal.** Buyers see their licences and sites, and download the latest or an earlier version of the
  software, without writing to you.
- **Customer list.** See who bought, with email, company and country.

### Email

- **Failover.** Licence emails go through a list of providers you order. If one fails, the next sends the email.
- **Providers.** WordPress mail (`wp_mail`) by default, plus SMTP, Twilio SendGrid, Mailgun, Postmark, Resend, Brevo,
  SMTP2GO and ZeptoMail. See [Email setup](/guide/email/).
- **Log and alerts.** A log of every email, regular checks of each provider, and an alert when one fails and when it
  recovers.

### Admin

- **One place to run it.** Products, orders, licences, activations, customers and the WooCommerce mapping all live
  under AccessBay in wp-admin, with a home screen that lists the next steps.
- **Search and saved views.** Search licences, customers, orders, products and settings from anywhere, and filter and
  save views on every list. See [Search and filters](/guide/finding-things/).
- **Light and dark.** Choose the look in the theme menu.

### REST API and WP-CLI

- **REST API.** Every route is under `accessbay/v1` and listed in the [API reference](/api/). Create API keys with
  limited scopes under AccessBay > API keys, and read [REST conventions](/developers/rest-conventions/).
- **WP-CLI.** `wp accessbay` commands cover products, prices, releases, customers, licences, health and keys, for
  scripts and for setting up a test site.

### Health and security

- **Health checks.** AccessBay checks its own setup (secret, database, background jobs, email, clock, file storage)
  and tells you in plain words how to fix a problem. See [Health checks](/guide/health-checks/).
- **A secret you control.** Stored keys and credentials are encrypted with a secret kept in your server
  configuration. See [The secret key](/guide/secret-key/).
- **No telemetry.** AccessBay and the SDK send no usage data to anyone. [Network calls](/developers/network-calls/)
  lists every request they make.
- **Your data stays.** Deleting the plugin keeps your records unless you opt in to remove them, and you can erase
  everything on purpose from the Settings screen.

### Free and Pro

Everything above is in the free plugin, which works fully on its own. AccessBay Pro is in development and adds no
working features yet, so nothing on this page is Pro only today. The planned Pro features are on the
[Roadmap](/roadmap/).

## Use cases

- **Sell a plugin with a yearly licence.** Create a software product with a price that allows a number of sites for a
  number of days. Buyers get a key, activate it in the plugin, and keep receiving updates while the licence is active.
- **Sell a theme.** The same flow works for a theme. Your theme asks your site for updates through the SDK.
- **Sell a licence for your own software.** Issue a key for each customer. Your software activates and validates it
  against your site through the licence routes in the [API reference](/api/), and the answers are signed.
- **Updates only for paying buyers.** Releases are offered only to sites with a valid licence, and a refund or dispute
  ends access.
- **Limit and manage sites.** Give each licence a number of seats, allow a few staging sites, and deactivate a site
  from the Activations screen when a customer moves.
- **Support a customer.** Look up an order, licence or customer from one search box, extend a licence, add a seat or
  replace a lost key.
- **Keep selling when email breaks.** Add a second provider, and a customer who just paid still sees their key on
  screen and by email.

## What comes next

Renewals, a checkout without WooCommerce, subscriptions and more are planned. See the [Roadmap](/roadmap/) for the
order, and [Known limits](/guide/known-limits/) for what this release leaves out.
