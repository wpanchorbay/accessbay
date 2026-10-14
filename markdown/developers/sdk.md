---
title: "SDK"
description: "Add AccessBay licensing to your own WordPress plugin with the SDK. Install it, prefix it, configure it, test it and release it."
---
The AccessBay SDK is a small PHP library you build into a WordPress plugin you sell. It activates a licence key
against your own AccessBay install, checks that every answer is signed by that install, and tells your plugin whether
the licence is active. It also supplies a licence screen, WP-CLI commands and a Site Health test, so you write none of
them.

**Status.** The licence client described here is built. Update delivery, where WordPress offers your customers a new
version and the SDK checks its signature and SHA-256 before installing it, **lands with the updater** and isn't in the
SDK yet. Sections that depend on it say so.

The SDK never talks to anyone but the AccessBay install you configure. The [network calls](/developers/network-calls/)
page lists every request it makes and every field it sends.

## Requirements

- PHP 7.4 or newer on the sites that run your plugin. The SDK uses no newer syntax.
- An AccessBay install of your own, reachable over `https`, with your product set up on it. Its address is your
  `server_url`, and the product's slug is your `product`.
- To build your plugin: Composer, PHP 8.1 or newer (Strauss needs it), and a network connection for Composer. The
  finished plugin still runs on PHP 7.4.

## Install with Composer

AccessBay has no public package registry, so you don't install the SDK with `composer require` from Packagist. Each
AccessBay release ships the SDK as a versioned zip beside the plugin zips: `accessbay-sdk-<version>.zip` and its
`.sha256` file. Check it with `sha256sum -c accessbay-sdk-<version>.zip.sha256`, then unpack it next to your plugin's
`composer.json`. You get one folder, `accessbay-sdk/`, holding `src/`, `composer.json`, `LICENSE`, `README.md`,
`CHANGELOG.md` and `example-plugin/`. Point a Composer `path` repository at that folder. The package name inside it is
`wpanchorbay/accessbay-sdk`, and its version (in `composer.json` and the newest `CHANGELOG.md` heading) is the SDK's
own, not the AccessBay plugin's. The example plugin in `example-plugin/` does the same, and so does the sample below.
Keep `accessbay-sdk/` out of your own release zip: your plugin ships only the copy Strauss prefixes. The
constraint `^0.1@beta` takes fixes within 0.1 and never a breaking 0.2 or 1.0; raise it when you move up.

## Prefix it with Strauss

Never ship the SDK under its own namespace. If two plugins on one site both did, PHP would load one copy for both. The
SDK would then refuse to run (see [Two vendors on one site](#two-vendors-on-one-site)). Strauss copies the SDK and its
one dependency, `paragonie/sodium_compat`, into a folder and renames their namespaces and classes to a prefix you choose.

Choose three prefixes that belong to you: a namespace, a class prefix and a constant prefix. This `composer.json` is the
example plugin's with a different vendor name:

```json
{
  "name": "acme/acme-pro",
  "type": "wordpress-plugin",
  "license": "GPL-2.0-or-later",
  "repositories": [ { "type": "path", "url": "./accessbay-sdk", "options": { "symlink": false } } ],
  "require": {
    "php": ">=7.4",
    "wpanchorbay/accessbay-sdk": "^0.1@beta"
  },
  "require-dev": { "brianhenryie/strauss": "0.30.*" },
  "config": { "sort-packages": true },
  "extra": {
    "strauss": {
      "target_directory": "vendor-prefixed",
      "namespace_prefix": "Acme\\Vendor\\",
      "classmap_prefix": "Acme_Vendor_",
      "constant_prefix": "ACME_VENDOR_",
      "delete_vendor_packages": true,
      "override_autoload": { "paragonie/sodium_compat": { "classmap": [ "src/" ] } },
      "exclude_from_copy": { "file_patterns": [ "#paragonie/sodium_compat/(lib|namespaced)/#", "#paragonie/sodium_compat/autoload#" ] }
    }
  }
}
```

Keep the `override_autoload` and `exclude_from_copy` lines. With Strauss's defaults, some of `sodium_compat`'s global
classes stay unprefixed.

Build it:

```sh
composer install
vendor/bin/strauss
```

Strauss's own autoloader doesn't list every class it writes. The example plugin therefore replaces it with a classmap
of the whole `vendor-prefixed/` folder. Run this in the folder that will become your plugin, after copying
`vendor-prefixed/` into it from the build folder:

```sh
rm -rf vendor-prefixed/autoload.php vendor-prefixed/composer
printf '{ "autoload": { "classmap": [ "vendor-prefixed/" ] } }\n' > composer.json
composer dump-autoload --classmap-authoritative
rm composer.json
```

That produces `vendor/autoload.php`, which your main plugin file requires. `example-plugin/build.sh` does all of this
and zips the result; copy it as the start of your own release script.

## Start the client and set the config

Call `Client::boot()` while your main plugin file loads, never on the `init` hook. This is the example plugin's whole
integration, with the Strauss namespace from above:

```php
<?php
/**
 * Plugin Name: Acme Pro
 * Version: 1.0.0
 * Requires PHP: 7.4
 * Update URI: https://updates.example.com/acme-pro/
 */

defined( 'ABSPATH' ) || exit;

require_once __DIR__ . '/vendor/autoload.php';

$acme_client = \Acme\Vendor\AccessBay\Sdk\Client::boot(
	array(
		'server_url'  => 'https://licences.example.com',
		'product'     => 'acme-pro',
		'public_keys' => array( 'your-kid' => 'base64 public key from your AccessBay install' ),
		'plugin_file' => __FILE__,
	)
);
```

Pass `server_url`, `product` and `public_keys` as a literal array, as above. Don't pass them through a filter: a filter
would let any snippet on the site swap your licence server or your signing keys.

`boot()` registers hooks and reads no option and runs no query on a front-end request. Calling it again for the same
plugin folder returns the first client.

| Key | Required | Meaning |
|---|---|---|
| `server_url` | yes | The origin of your AccessBay install, such as `https://licences.example.com`. It must start with `https://`. `http://` is accepted only for a local server (loopback or private address) when the site's environment type is `local` or `development`. |
| `product` | yes | Your product's slug on the install: lower-case letters, digits, `_`, `.` and `-`, starting with a letter or digit, at most 64 characters. |
| `public_keys` | yes | A map of key id to the base64 Ed25519 public key. See [The public key](#the-public-key). |
| `plugin_file` | yes | The absolute path of your main plugin file. `__FILE__` in that file is right. |
| `cli_command` | no | The WP-CLI command name. Defaults to the product slug. |
| `admin_page` | no | `true` (the default) shows the SDK's licence screen. `false` hides it so you can build your own on the client's methods. |
| `offline_grace_days` | no | Whole days from 1 to 30, default 7. How long the plugin keeps a licence usable when the server can't be reached. It can only shorten the deadline the server signed, never lengthen it. |
| `messages` | no | Your own wording for the SDK's screen texts, as a map of message id to text, for example `'page_title' => 'Acme Pro licence'`. |
| `mode` | no | `live` (the default), or `test` when `server_url` is a sandbox install. A live client refuses a test licence's answer, and the reverse. |
| `meta_url` | no | Lands with the updater. Not read yet. |

**A bad config sends nothing.** If a required value is missing or malformed, `server_url` isn't `https`, the plugin
file can't be read, or a public key isn't 32 bytes once decoded, `boot()` returns a client with the status
`unconfigured`. That client makes no network request, every method returns a failed result naming the key at fault, and
administrators see an error notice on the Plugins screen. Each request that boots it writes one line to the PHP error
log. In a `local` or `development` environment with `WP_DEBUG` on, `boot()` throws a `ConfigException` instead, so you
can't miss the mistake. It never throws on `staging` or `production`: a notice is a smaller problem for your customers
than a blank screen.

## Update URI

Put an `Update URI` header on your main plugin file, with an `https` address on a domain you control, as the example
does. WordPress uses the header's host to choose which filter offers updates for the plugin, and the updater uses the
same host as the default place to read the public release information, unless you set `meta_url`. **This lands with the
updater.** The licence client in this release doesn't read the header, so nothing happens yet when you add it.

## The public key

Your AccessBay install signs every answer. The SDK accepts an answer only if it verifies against a key you pinned in
your plugin, and it never fetches a key.

On the AccessBay install, run:

```sh
wp accessbay keys show
```

Among the lines it prints is `Signing key:` followed by JSON holding `kid`, `alg` and `pk`. Put `kid` and `pk` into
`public_keys` as `array( 'kid' => 'pk' )`. An answer signed with a key id that isn't in your map is refused.

The map can hold more than one entry. To rotate a key without a gap, ship a release that carries both the current
and the next key before your install starts signing with the next one.

## Use the licence in your plugin

`boot()` returns the client. Keep it where the rest of your plugin can reach it.

```php
$acme_client->status()->code;            // active, grace, lapsed, inactive, invalid or unconfigured
$acme_client->entitled( 'acme-pro' );    // true when the signed entitlements include this slug
$acme_client->updates_allowed();         // true only when the status is active or grace
$acme_client->masked_key();              // the stored key with only its last four characters visible

$acme_client->on_status_change(
	function ( $current, $previous ) {
		// Called whenever the status code changes. Both arguments are Status objects.
	}
);
```

`status()` also gives you `reason` (for display only), `checked_at`, `access_until`, `grace_until`, `expires_at` (the licence's own end in unix seconds, `0` for no expiry), `entitlements` and
`staging`. `activate( $key )`, `deactivate()` and `validate()` each return an `Outcome` with `ok`, `status`, `code`,
`message`, `retry_after` and `server_code`. A refused key, a full licence or a network failure comes back as an
`Outcome` and never throws.

Gate features on `entitled()`, not on `status()`. A lapsed licence stops updates and support, not features the customer
already bought. `entitled()` stays true through `active`, `grace`, `lapsed` and any network failure. It turns false
only when your install reports the licence revoked, held or void, or when the site never had a verified activation.
To switch features off when your install reports a licence revoked, held or void, check for `status()->code === 'invalid'`.

Only signed fields decide the status. The status, seat counts and messages in the server's answer that aren't signed
are shown to the user and never grant access.

## The licence screen

With `admin_page` left on, the SDK adds one page under **Settings**. By default it is named **Licence (your-product-slug)**. It has:

- the licence **Status** and, once activated, **Expires** (a date, or "No expiry"); Site Health adds **Verified until**;
- the key, shown masked, with **Reveal key**, **Activate** and **Deactivate** buttons;
- a note, **Staging site: this activation does not use a seat.**, when your install counted the site as staging.

Only users who can manage options see it, and every action checks a nonce first. A row under your plugin on the Plugins
screen links to the page whenever the licence isn't active. The SDK adds no top-level menu, dashboard widget or
redirect. Its notices appear on the Plugins screen and on its own page, and nowhere else.

The SDK also registers, for each copy:

- WP-CLI: `wp your-command license status`, `activate <key>`, `deactivate` and `recheck`. Use `activate -` to read the
  key from standard input, because a key given as an argument shows in the process list. `recheck` forces a licence
  check now. A command that fails exits with status 1 and prints the result code, never the key. `your-command` is your
  `cli_command`.
- A Site Health test that reports the licence status and expiry, and a debug section. Neither shows the key.
- A daily check of the licence that is planned once a key is stored.

Translate the SDK's texts in your own text domain, using the `messages` key. The SDK can't use yours.

### Uninstall

Deactivating your plugin stops the daily check and keeps the key. To clean up when the plugin is deleted, call the
SDK from your `uninstall.php` with the same config:

```php
<?php
defined( 'WP_UNINSTALL_PLUGIN' ) || exit;

require_once __DIR__ . '/vendor/autoload.php';

\Acme\Vendor\AccessBay\Sdk\Uninstaller::run(
	array(
		'server_url'  => 'https://licences.example.com',
		'product'     => 'acme-pro',
		'public_keys' => array( 'your-kid' => 'base64 public key from your AccessBay install' ),
		'plugin_file' => __DIR__ . '/acme-pro.php',
	)
);
```

`Uninstaller::run()` asks your install to free the site's seat (waiting at most 5 seconds, and carrying on if it
can't), deletes the SDK's options and the daily check, and never blocks the uninstall. Don't also register an uninstall
hook for the SDK.

## Two vendors on one site

A site can run plugins from several vendors, each with its own copy of the SDK. Two things keep the copies apart.

**Your prefix.** Strauss gives your copy its own namespace, class prefix and constant prefix, so `Acme\Vendor\...` and
another vendor's `Zeta\Vendor\...` are different classes to PHP. The SDK defines no global function, constant or class
of its own. The one name Strauss doesn't change is the global `SodiumException` class, which both copies share and which
holds no state.

**Names from the plugin folder.** Everything the SDK registers with WordPress is named from the folder your plugin
lives in, not from your product. The prefix `{p}` is `abs_` followed by the folder name with every run of characters
other than letters and digits turned into `_`, in lower case. When that changed the folder name, a short hash of the
original name follows, so `a-b` and `a_b` stay apart. Two plugins can't share a folder on one site, so two copies never
share a name, whatever `product` each configures.

| What | Name |
|---|---|
| Stored key | option `{p}_key` |
| Licence state | option `{p}_state` |
| Update cache | option `{p}_meta` |
| Request lock | option `{p}_lock` |
| Daily check | scheduled event `{p}_recheck` |
| Licence page | `{p}-license` |
| Site Health test | `{p}_license` |
| WP-CLI | `wp {cli_command} license`, left alone with a warning if another copy already registered that name |

The SDK stores no transients and no table, and its options aren't autoloaded.

An unprefixed copy is caught, not trusted. If the SDK's code didn't come from your own plugin folder, because PHP's
autoloader served another plugin's copy, the client turns `unconfigured` and tells the administrator to prefix the SDK.

**Renaming your plugin folder changes `{p}`.** The renamed copy starts with no key and no state, so your customers
must enter their key again. A download from GitHub that adds `-main` to the folder name, or a versioned folder name,
does exactly that. Ship the same folder name in every release.

The SDK's tests build the example plugin twice with different prefixes, load both copies in one process, and check that
they share no names and don't touch each other's options.

## Test on a staging site

Do this before every release, on a WordPress site that isn't your customers'.

1. Build the zip with your release script and install it from **Plugins > Add New > Upload Plugin**. Set the site's
   environment type in `wp-config.php`, for example `define( 'WP_ENVIRONMENT_TYPE', 'staging' );`. The SDK sends the
   environment type with the activation as a hint.
2. Check the Plugins screen. A config error shows as an error notice. If you see one, fix the config key it names and
   stop here.
3. Open **Settings > Licence (your-product-slug)**, paste a test key for your product, and press **Activate**. The
   status should read **Active**, with an **Expires** date, or "No expiry".
4. Run `wp your-command license status`. It prints `status`, `access_until`, `staging` and the masked `key`.
5. Press **Reveal key** and check it shows the key you pasted. Open **Tools > Site Health** and find the licence test:
   it shows the status and expiry and no key.
6. Cut the site's connection to your install, for example with a firewall rule, and run
   `wp your-command license recheck`. It fails with the code `abs_transport`, the status stays as it was, and your
   plugin's `entitled()` checks still pass. Restore the connection and run `recheck` again.
7. Press **Deactivate**. The status returns to **Not activated**.
8. If you run a sandbox install, repeat steps 3 and 4 against it with `'mode' => 'test'`.

Testing on a local server over `http://` works only when the environment type is `local` or `development`.

## Release checklist

Before each release:

- [ ] The release script runs Strauss with your own three prefixes and the `override_autoload` and
  `exclude_from_copy` lines from this guide, and rebuilds the classmap autoloader.
- [ ] No namespace in `vendor-prefixed/` outside your own prefix: `grep -rhE '^namespace ' vendor-prefixed | grep -vF 'Acme\Vendor'` prints nothing (with your prefix in place of `Acme\Vendor`).
- [ ] `server_url`, `product` and `public_keys` are literal values, not read from a filter, a setting or a constant.
- [ ] `public_keys` holds the key id and key that `wp accessbay keys show` prints on your install today.
- [ ] The main plugin file has the `Update URI` header (lands with the updater).
- [ ] The plugin folder in the zip has the same name as in the last release.
- [ ] Your features check `entitled()`, not `status()`, so a lapse or an outage doesn't switch them off.
- [ ] `uninstall.php` calls `Uninstaller::run()` with the same config, and your plugin doesn't also register an
  uninstall hook for the SDK.
- [ ] Your plugin's privacy text lists the calls on the [network calls](/developers/network-calls/) page.
- [ ] The staging test above passed on the zip you are about to ship.
- [ ] The WordPress.org build, if you have one, has no SDK and no updater: no `Update URI` header other than
  WordPress.org's, no `update_plugins_` and no `upgrader_pre_download`. Check it with
  `grep -rnE 'Update URI:|update_plugins_|upgrader_pre_download|plugin-update-checker|PucFactory|AccessBay.*Sdk' <build folder>`,
  which should print nothing.
