---
title: "Plugin settings"
description: "Show AccessBay in the admin bar, choose whether deleting the plugin deletes its data, and erase all AccessBay data while keeping the plugin."
---
The Plugin section of AccessBay's settings holds the settings about the plugin itself rather than your store. Both
settings are off until you turn them on.

## AccessBay in the admin bar

Turn this on to add an AccessBay link, with the AccessBay icon, to the bar at the top of the WordPress admin.
It opens the AccessBay home. It shows only to people who may open AccessBay, and only in the admin, not on your site's
pages.

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
