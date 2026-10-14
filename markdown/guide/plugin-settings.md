---
title: "Plugin settings"
description: "How the Settings screen works, what every setting does, how to show AccessBay in the admin bar, and how to erase all AccessBay data."
---
## The Settings screen

Go to AccessBay > Settings. The screen has sections: **General**, **Email settings**, **Network**, **Logging**,
**Licensing** and **Plugin**. API keys and Email providers have their own entries in the sidebar. To jump straight to a
setting, [search for it](/guide/finding-things/#settings-in-the-results).

- A setting that is on or off is a full-width row: its name and help text on the left and a switch on the right.
  Select anywhere on the row to flip it.
- Nothing changes until you select **Save changes**. AccessBay then shows "Settings saved".
- If you leave the screen with unsaved changes, AccessBay asks "Leave without saving?".

## All settings

| Section | Setting | What it does |
|---|---|---|
| General | Mode | Test marks new orders and licences as test ones, so they stay apart from real ones. Live is for real customers. |
| General | New order origin | Where new orders start: WooCommerce, AccessBay's own checkout, or both while you switch over. |
| General | Archived products | Keep updates: customers with a valid licence keep downloads and updates after you archive a product, until their licence expires. Stop updates: archiving ends downloads and updates for everyone. Archiving always ends new sales. |
| General | Event retention | How many days events are kept before they are deleted (1 to 3650). |
| General | Queue warning | Minutes a queued job may wait before the background-jobs check warns (1 to 59). See [Health checks](/guide/health-checks/). |
| General | Customer portal page | The ID of the page that shows the customer portal. 0 means none. |
| General | Support email | The address customers see on the thank-you page and in the portal when they need help. Empty shows none. |
| Email settings | Email record retention | Days to keep AccessBay emails, their attempts, provider events and the site mail log. 0 keeps everything (0 to 3650). |
| Email settings | Email retry period | Days a failed email keeps being retried before it is given up (1 to 5). |
| Email settings | Staging email allowlist | On a staging site, email goes only to these addresses and to site administrators. |
| Email settings | Send a daily test email | Once a day, send a short test email through each mail provider. Off by default. |
| Email settings | Daily test email recipient | Where the daily test email goes. Empty uses the site administrator address. |
| Email settings | Mail problem alerts | Who is emailed when a mail provider stops working and again when it works, up to ten addresses. Empty uses the site administrator address. |
| Network | Trusted proxies | Your own proxies, one IP address or CIDR range per line. Their forwarded client address is trusted. |
| Network | Behind Cloudflare | Trust Cloudflare's forwarded client address. |
| Logging | Request log retention | Days to keep request log rows (1 to 90). |
| Logging | Request log size | The most rows the request log keeps, the oldest going first (1000 to 1,000,000). |
| Licensing | Access deadline cap | The longest an access deadline may be set ahead, in days (1 to 365). |
| Licensing | Licence key prefix | The letters every new licence key starts with. |
| Licensing | Staging activations | The most staging sites a licence may keep active at once. 0 turns the allowance off (0 to 1000). |
| Plugin | Delete data when the plugin is deleted | See below. |
| Plugin | AccessBay in the admin bar | See below. |

The [email setup](/guide/email/) page explains the email settings in context, and
[Health checks](/guide/health-checks/) explains Queue warning.

## The Plugin section

The Plugin section holds the settings about the plugin itself rather than your store. Both settings are off until you
turn them on.

## AccessBay in the admin bar

Turn this on to add an AccessBay link, with the AccessBay icon, to the bar at the top of the WordPress admin. It opens
the AccessBay home.

- After you select **Save changes**, the link appears or disappears at once. You don't need to reload the page.
- It shows only in the admin, never on your site's pages.
- It shows only to people who may open AccessBay.
- The icon takes its colours from the admin colour scheme the person chose in their WordPress profile, including the
  hover colour, and it follows a browser's forced-colours mode.

## Delete data when the plugin is deleted

When you delete AccessBay from the Plugins screen, it always stops its background jobs and removes its temporary data.
Your products, licences, orders and customers stay, so you can install AccessBay again and carry on.

Turn this setting on if you want deleting the plugin to remove everything instead: AccessBay's database tables, its
settings, its products, its licence and API keys, its secret key file and its stored files. This cannot be undone, so
make a backup first. Only site administrators see this setting. On a multisite network only super admins see it, and
it applies to one site at a time. The folder of stored files stays while another site on the network uses the same
folder.

You can also opt in for every site at once by setting `ACCESSBAY_REMOVE_ALL_DATA` to `true` in your site's
configuration file.

## Erase all data

Erase all data removes the same things as deleting the plugin with the setting above turned on, but keeps AccessBay
installed and active. AccessBay then sets itself up again, as on a fresh install: it creates its tables again, gives
the default roles their capabilities back and runs its health checks again. Use it to start over, for example after
testing on a staging site.

- Only a site administrator can do it. On a multisite network only a super admin can.
- You type `ERASE` to confirm and enter your own WordPress password. A wrong password erases nothing, and the audit log
  records the attempt (never the password). After five tries in 15 minutes you wait before you can try again.
- If AccessBay made a secret key file for your site, it makes a new one. Download its new recovery key afterwards
  ([The secret key](/guide/secret-key/)). A secret you set in your site's configuration file stays as it is.
- AccessBay goes back to test mode and its default settings.
- If AccessBay Pro is active, its data (saved payment methods) is erased too.
- On a multisite network only the current site is erased. The folder of stored files stays when another site on the
  network uses the same folder, and the audit log says so.
- The erase is recorded in AccessBay's audit log, with who did it and when.

If something stops the erase partway, AccessBay says so and records it in the audit log when it can. Run it again to finish; running it twice does no harm.
