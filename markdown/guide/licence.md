---
title: "Licence"
description: "The licences AccessBay and its client SDK are released under, what they mean for plugins you build with the SDK, and how to get the source."
---
AccessBay's plugins are released under the GNU General Public Licence, version 3 or later (GPL-3.0-or-later). The client SDK that you include in the plugins and themes you sell is released under version 2 or later (GPL-2.0-or-later). This page says what each licence covers and what we have not settled. It is not legal advice.

## The AccessBay plugins

The free AccessBay plugin, the AccessBay Pro plugin, and the code compiled into them are GPL-3.0-or-later. Each plugin's header and `readme.txt` say so.

We chose version 3 because the free plugin bundles Action Scheduler, which is GPL-3.0-or-later and can't be distributed under version 2.

What this means for you as a vendor:

- You may use, modify and redistribute the plugins under the GPL. Anyone who receives a copy may do the same.
- The GPL grants no trademark rights. A redistributed or modified copy can't call itself AccessBay.
- Pro is GPL as well. What you pay for is updates, support, licence-key activation and hosted features, not the right to use the code.

## The client SDK in your own plugins

The SDK is released under GPL-2.0-or-later. We chose the widest GPL variant on purpose: the SDK is embedded in other people's plugins, and a plugin released under any GPL licence can include it. The SDK doesn't bundle Action Scheduler or any Apache-2.0 code, which is why it doesn't need version 3.

The SDK package is meant to be prefixed with Strauss when you include it in your plugin. The [SDK page](/developers/sdk/) will carry the full steps when the SDK is released.

A plugin that includes the SDK is subject to the GPL. When you distribute it, you distribute a combined work, and the whole of it has to go out under terms that are compatible with the GPL. This page is not legal advice. Ask a lawyer how it applies to your own plugin.

## Where the source is

- **PHP:** the plugin's own PHP files ship in the zip as they are in the repository. Strauss gives the classes of most bundled libraries a prefix. Action Scheduler is the exception: the free plugin bundles it unprefixed. A few development files, such as tests, build configuration, `composer.lock` and the UI demo page, are left out.
- **Admin screens:** the zip carries the compiled JavaScript for the admin screens, without its human-readable source. Putting that source in the zip, through source maps or a link, is planned and has not been done yet.
- **How to get the source today:** each zip is offered for download with a source archive for the same version, as a separate download beside it. If you can't find the archive for the version you hold, write to {source request address} with that version and we send it to you.
- **The repository:** it is private, so it is not a public source link.
