---
title: "Installation"
description: "Install and activate AccessBay, what activation sets up, and what deactivating or deleting it removes."
---
AccessBay has no public release yet. When you have the plugin's zip file, install it like any other WordPress plugin.

## Install and activate

1. Check the [requirements](/guide/requirements/).
2. In wp-admin, go to **Plugins → Add New Plugin → Upload Plugin**, choose the zip file and select **Install Now**.
   Or copy the `accessbay` folder into `wp-content/plugins/` on the server.
3. Select **Activate**.

<!-- screenshot: the Plugins screen with AccessBay active and the AccessBay menu in the wp-admin sidebar -->

If the server is missing something AccessBay needs, activation stops with a page that lists each problem in plain
words, such as "AccessBay needs the PHP openssl extension". Nothing has been written to the database at that point:
fix the problem and activate again.

## What activation sets up

- AccessBay's database tables (their names start with your table prefix and `accessbay_`).
- Its capabilities. Administrators get all of them. Shop managers (a WooCommerce role) get the ones to manage
  AccessBay, manage orders and view reports, but not refunds, revealing licence keys, publishing releases or changing
  settings. If you remove a capability from a role, AccessBay does not add it back.
- The **AccessBay** menu in wp-admin, for users who can manage AccessBay.
- Every health check, due to run straight away ([Health checks](/guide/health-checks/)).

Next, set [the secret key](/guide/secret-key/).

## The customer portal page

Buyers download their files and manage their licence keys on a **customer portal page**: a normal WordPress page that
holds the AccessBay **Customer portal** block (or the `[accessbay_portal]` shortcode). The page is what the thank-you
page, the My Account menu item, the key email and the key recovery emails link to. Without it none of those links
appear, and a buyer has no way in.

- **Created for you.** The first time an administrator opens wp-admin after AccessBay is activated or updated, it adds
  a published page titled **Customer portal** with the block, and chooses it in **Settings → General → Customer portal
  page**. If a page already holds the block or the shortcode, AccessBay chooses that one instead; it never makes a
  second page. If you already chose a page, it leaves it alone.
- **Your choice wins.** AccessBay creates the page once. If you delete or trash it, or clear the setting, it does not
  make another. Choose a page again under **Settings → General**, or restore the one you deleted.
- **Health warning.** The **AccessBay customer portal page** test in **Tools → Site Health** warns when no page is
  chosen, when the page was deleted, is not published, or no longer holds the portal block or shortcode.
- **How buyers download.** The key email includes a link to the portal. There a buyer signs in, or asks for a sign-in
  link by email, and sees their licence keys and the downloads that go with them.

A key recovery or account claim email can only be sent while a portal page is chosen. If one is attempted without it,
the email is marked failed once, with an alert in the [health results](/guide/health-checks/) naming the cause, and is
not retried.

## Deactivating and deleting

Deactivating AccessBay stops its scheduled jobs and keeps everything else.

Deleting it removes only what is safe to lose: its scheduled actions and temporary data. Your tables, settings,
licences, orders, customers and user capabilities stay in the database, so a store's records don't vanish because
someone clicked **Delete**.

To remove all AccessBay data when you delete the plugin, opt in first, in either of two ways:

- Add `define( 'ACCESSBAY_REMOVE_ALL_DATA', true );` to `wp-config.php`.
- Set the option `accessbay_remove_data_on_uninstall` to true, for example with
  `wp option update accessbay_remove_data_on_uninstall 1`. On a multisite network the option is read for each site.

With the opt-in, deleting AccessBay drops its tables, deletes its `accessbay_*` options and user settings, and removes
its capabilities from every role. Other plugins' tables and options are never touched. This can't be undone, so take a
backup first.
