---
title: "Changelog"
description: "What changed in each version of AccessBay and its SDK, newest first."
---
This page lists what changed in each release of AccessBay and the AccessBay SDK, newest first.

## AccessBay 1.0.0

First public release.

- Licence keys, activations and seats, created from paid WooCommerce orders and reversed on refund or dispute.
- Update delivery for the software you sell, with signed manifests and short-lived download links.
- The AccessBay SDK as a versioned zip.
- Admin screens for the home overview, products and prices, releases, licences, activations, customers, orders, the WooCommerce mapping, the email log, API keys, settings and health.
- Email delivery through `wp_mail` by default, with optional SMTP, Brevo, Resend, Postmark, SMTP2GO, ZeptoMail, Mailgun and SendGrid providers. Failover between providers, and a log of every email and every site-wide `wp_mail` send.
- A test email to any address through one provider, hourly checks of each provider and an optional daily test send. Alerts appear on the AccessBay screens and by email when a provider fails and when it recovers.
- The licence key on the WooCommerce thank-you page and in email, and a customer portal with licences, sites and downloads.
- WP-CLI commands and a REST API with scoped API keys.
- One filter row on the Products, Orders, Licences and Activations screens, with search, filter menus that take several values at once, Clear filters, and saved views in a Views menu. These replace the status tabs. The email log tabs get filters too.
- Search that also finds settings. Open the search dialog with the `/` key, and a result for a field opens its section and puts the cursor in the field.
- The admin has no top bar on larger screens. Search sits under the logo and the theme toggle sits beside the Docs link. A screen inside another shows its parent as a dimmed link in front of the title, as in "Products > Add product".
- Every health check explains its result in plain words. Warnings and critical results come with steps: links to the right admin page, and WP-CLI commands and `wp-config.php` lines you can copy. The Overall card counts and lists what needs attention.
- Yes/no settings are full-width rows, and the "AccessBay in the admin bar" link appears or disappears as soon as you save.
- The email provider picker shows the services as cards with their logos and puts WordPress (`wp_mail`) first, for sites where another SMTP plugin should send. The SendGrid provider is named Twilio SendGrid (API). SMTP2GO accepts an optional bearer token for its webhook.
- The REST list routes that back these screens take several values for one filter, as an array or a comma-separated list. Values in one filter are alternatives, and different filters combine.

## SDK 0.1.0-beta.1

- The first versioned zip of the SDK, released with each AccessBay release. There is no public package registry: unpack the zip and use it through a Composer `path` repository.
- A licence client to activate, deactivate and validate a key against your own AccessBay install. It accepts only answers signed by a public key you pinned.
- An updater: WordPress offers a new version of your plugin from a signed release document, and the SDK checks the package's size and SHA-256 before it is installed.
- A licence screen, a Site Health test, WP-CLI commands and an uninstaller, so a plugin does not need to write them.
- On the licence screen, a "Licence" link on the plugin's row on the Plugins screen, and a "Receive beta updates" switch. A defined `{PREFIX}_CHANNEL` constant still wins and locks the switch.
- The licence screen's title is your `page_title` message or else the plugin's own name.

New versions are added here as they are released.
