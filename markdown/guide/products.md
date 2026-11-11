---
title: "Products"
description: "The Products screen, its columns and filters, and what the Draft, Active and Archived statuses mean."
---
Go to AccessBay > Products to see everything you sell: plugins, downloads and bundles. The screen works like the other
lists, so search, filters, saved views and shareable links are described in
[Search and filters](/guide/finding-things/). Select **Add product** to create one.

Until you add the first product the screen says "No products yet". Add the first plugin, download or bundle you sell.

## Columns

| Column | What it shows |
|---|---|
| Product | The product's name. Select it to open the product. |
| Type | Software, Digital download or Bundle. |
| Status | Draft, Active or Archived. |
| Prices | How many prices the product has. |

Click **Product** to sort by name. You can hide the Type, Status and Prices columns with the columns button.

## Filters and search

- **Status:** Draft, Active, Archived. Tick more than one to see several at once.
- **Type:** Software, Digital download, Bundle.

The search box matches part of a product's name or slug.

## Statuses

- **Draft.** Customers can't buy it. A new product starts here.
- **Active.** Customers can buy it. An active product with no active price can't be bought yet, and the product screen
  says to add a price on its **Pricing** tab.
- **Archived.** Customers can no longer buy it. Its prices, orders and licences stay, and it can't be activated again.
  Whether existing customers keep their downloads and updates depends on the **Archived products** setting under
  Settings, General.

The product screen has three tabs: **Details**, **Pricing** and **Releases**. The title line shows "Products" as a dimmed
link before the product's name, which takes you back to the list.

## The plugin details popup

Customers who run a plugin you host see "View version x.y details" beside an update on the Plugins screen and on
Dashboard > Updates. That popup shows your plugin's `readme.txt`, the way a plugin on wordpress.org does.

Ship a `readme.txt` in the format wordpress.org uses in the plugin's zip, at the top level of the plugin folder. When you
upload a release, AccessBay reads it:

- Each `== Section ==` becomes a tab of the popup. `== Description ==`, `== Installation ==`,
  `== Frequently Asked Questions ==` and `== Changelog ==` are the usual ones; any other section you write is kept.
- The header lines give the popup its **Requires at least**, **Tested up to** and **Requires PHP** values.
- Inside a section, `= Heading =` lines, `*` and `-` lists, numbered lists, paragraphs, `**bold**`, `*emphasis*`,
  `` `code` `` and `[text](https://example.com)` links work. Other HTML is shown as plain text, and a link must start with
  `https://`, `http://` or `mailto:`.
- A section you leave out has no tab. The popup has no download button: updates still install from the Plugins screen.

The popup reads the same public release information as the update check, so it works for customers whose licence has
lapsed. If AccessBay can't be reached, WordPress shows its own default popup instead.
