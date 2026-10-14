---
title: "Network calls"
description: "Every request AccessBay and the AccessBay SDK make over the network, when they make it, and what each one sends."
---
**AccessBay and the SDK send no telemetry.** They send no usage statistics, analytics or crash reports to anyone, and
nothing is sent to any server run by the people who built AccessBay. This page lists every HTTP request the code
makes in this release, so you can check that for yourself and copy the list into your own privacy text.

There are five kinds of request:

| Who sends it | Where it goes | Why |
|---|---|---|
| The SDK inside a vendor's plugin | The vendor's own AccessBay install, at the `server_url` the vendor configured | Activate, check and deactivate a licence, and ask for an update package |
| The SDK's updater inside a vendor's plugin | The host in the plugin's `Update URI` header (or the `meta_url` the vendor configured), and the address the install returns for a package | Read the release information and download an update |
| The AccessBay plugin | The same WordPress site it runs on | Health checks |
| Action Scheduler, a library bundled in the AccessBay plugin | The same WordPress site it runs on | Start the background job runner |
| The AccessBay plugin | `www.cloudflare.com`, only when an administrator runs a command | Refresh the list of Cloudflare's address ranges |

Before the AccessBay plugin sends a health check or the Cloudflare request, it looks the host name up through your
server's DNS resolver. Those names are the site's own name and `www.cloudflare.com`, and your resolver sees them.

The updater's requests are listed in their own section near the end.

## The SDK's calls to your AccessBay install

The SDK is a library a vendor builds into a plugin. These calls come from the licence client in that plugin, to the
address its vendor configured as `server_url`. If the plugin isn't configured correctly, the client is `unconfigured`
and makes no call at all.

Every call in this section is a `POST` with a JSON body to `{server_url}/?rest_route=/accessbay/v1/{route}`. This form works on every
permalink setting. The licence key and the activation token travel in the body or in a header and never in the URL.

Every call sends these headers: `Accept: application/json`, `Content-Type: application/json` and
`User-Agent: {product}; accessbay-sdk/{sdk version}`, where `{product}` is the vendor's product slug. A call waits up
to 10 seconds for an answer (15 at most), follows no redirect and reads at most 1 MiB. The SDK never turns off
certificate checks.

| Call | Route | Fields sent in the body | When |
|---|---|---|---|
| Activate | `licenses/activate` | `license_key`, `product`, `site_url`, `environment`, `plugin_version`, `nonce` | When someone presses **Activate** on the licence screen, runs `wp your-command license activate`, or the vendor's code calls `activate()` |
| Validate | `licenses/validate` | `license_key`, `product`, `site_url`, `plugin_version`, `nonce` | About once a day after a key is stored, when someone runs `wp your-command license recheck`, or when the vendor's code calls `validate()` |
| Deactivate | `activations/deactivate` | `activation_token` | When someone presses **Deactivate**, runs `wp your-command license deactivate`, or deletes the plugin and its `uninstall.php` calls the SDK's `Uninstaller` (this one waits 5 seconds at most) |

What the fields hold:

- `license_key`: the licence key the site owner entered.
- `product`: the vendor's product slug.
- `site_url`: the site's home URL, from WordPress's `home_url()`.
- `environment`: the site's WordPress environment type: `local`, `development`, `staging` or `production`. It is a
  hint, and the server decides for itself whether a site is staging. Activate only.
- `plugin_version`: the `Version` header of your plugin, such as `1.4.2` or `2.0.0-beta.1`. The vendor's install stores
  the last one each activation reported, so the vendor can see which versions run in the wild. It is left out when
  the header is not a plain version string (digits and dots, with an optional `-tag`, at most 32 characters).
- `nonce`: 24 random bytes, written in base64url, made fresh for every request and kept in memory until the answer
  arrives. The server signs it into its answer, which is how the SDK knows the answer belongs to that request.
- `activation_token`: the token the server handed out when the site was activated.

The signed answer carries `expires_at`: the licence's own end as a positive integer in unix seconds, or `null` for a
licence with no expiry. The SDK refuses an answer whose `expires_at` is zero or negative. Servers older than this field
leave it out.

The daily check is a WordPress scheduled event planned for 24 hours after the last one plus a random 0 to 2 hours. It
is planned once a key is stored, and deactivating the plugin removes it. A validate that repeats within 60 seconds of
a good one is answered from the stored result and sends nothing. After a failed validate, the daily check waits
before it tries again, 5 minutes at first and growing to 6 hours, plus up to 20% random extra on each wait but never
more than 6 hours in all. If the server sends a `Retry-After` header, the wait is that long instead, capped at 6 hours,
with no extra. Nothing is sent while it waits, unless someone forces a check with `recheck`.

The SDK doesn't call the server's heartbeat route and sends nothing else: no list of plugins, no WordPress or PHP
version, no user names or email addresses.

## The AccessBay plugin's health checks

AccessBay checks its own setup with requests to **the site it runs on**. These requests reach only the site's own
scheme, host and port, follow no redirect, wait 5 seconds at most and read at most 64 KB. They go through the site's
public address, so a cache or CDN in front of the site sees them, which is what the checks look for. The cache check
doesn't send its requests when the site's name points at a private address, and reports the result as unverified.

| Check | Request | Sent |
|---|---|---|
| Cache in front of the site | Two `GET` requests, 2 seconds apart, to the site's `accessbay/v1/health/cache-canary` route | The headers `Accept: application/json` and `User-Agent: AccessBay`. No body. |
| File storage not reachable from the web | One `GET` to the same canary route, then, after AccessBay writes a short-lived test file into its storage directory, one `GET` for each public address that file could be served from. AccessBay deletes the file afterwards. | The same headers. No body. |

Each check is due once a day. A due check runs at the end of an administrator's request, a WP-Cron request or a WP-CLI
command, or after a visitor's response has already gone out. `wp accessbay health` runs every check now.

## The background job runner

AccessBay runs its recurring jobs through Action Scheduler, a library that ships inside the plugin. When anyone
loads a WordPress admin page, or the site receives an `admin-ajax.php` request, and a job is due, Action Scheduler
starts its queue runner with one request, at most once a minute. The request is a `POST`, sent without waiting for an answer, to **your own site's**
`admin-ajax.php`. It carries the action name `as_async_request_queue_runner`, a WordPress nonce and the cookies of the
request that triggered it, including that user's login cookies if they are logged in. Action Scheduler doesn't check the
certificate on this request by default. It carries no licence key or other licence data, and it never leaves your site.

## The Cloudflare address ranges

AccessBay ships with a list of Cloudflare's address ranges. It never refreshes the list by itself. When an
administrator runs `wp accessbay proxies refresh`, it sends two `GET` requests: `https://www.cloudflare.com/ips-v4` and
`https://www.cloudflare.com/ips-v6`. Each carries the header `User-Agent: AccessBay` and nothing from your site.
Cloudflare sees the address of your server, as any web server would. AccessBay keeps the previous list if an answer is
empty or malformed.

## Not network calls of AccessBay

- **Email.** AccessBay sends mail through WordPress's own `wp_mail()`, so whichever SMTP or mail service your site is
  set up to use carries it. What that service receives is between your site and that service.
- **WordPress itself.** Requests made by WordPress core or by other plugins, such as update checks for WordPress and
  for plugins hosted on WordPress.org, aren't made by AccessBay or the SDK.

## The SDK's updater

The updater is part of the SDK. It hooks WordPress's own plugin update check for the host in the plugin's `Update URI`
header, so it has no timer of its own: the first two calls below happen inside WordPress's update check (twice a day,
and when someone opens the Plugins screen), and the rest when an update is installed, by hand or automatically. If the
site disallows file changes (`DISALLOW_FILE_MODS`), the install steps send nothing.

| Call | Where | Sent | When |
|---|---|---|---|
| Read the release information | `GET {meta_url}{product}/latest.json`. `meta_url` defaults to `https://{host of the plugin's Update URI}/meta/`. | No key, no site URL and no body. When the site's owner opted in to beta (see [Release channels](#release-channels)) the file is `latest-beta.json`. The headers `Accept: application/json` and `User-Agent: {product}; accessbay-sdk/{sdk version}`. It waits 10 seconds at most, follows no redirect and reads at most 64 KiB. | Inside the update check, unless a failed read (no answer, a 429 or a 5xx) is still being waited out |
| Check the licence first | The Validate call above | As above | Before that read, if the last verified answer is more than 12 hours old and the SDK isn't waiting after a failure |
| Ask for the package | `POST {server_url}/?rest_route=/accessbay/v1/packages` on your AccessBay install | The header `X-AccessBay-Activation` with the activation token, and the same `Accept`, `Content-Type` and `User-Agent` headers as the other calls. No body, except `{"channel":"beta"}` when the site's owner opted in to beta. It waits 10 seconds at most and follows no redirect. | When an update is installed, and only if the licence allows updates |
| Activate again, then ask once more | The Activate call above, then the previous call again | As above | Only when the server answers 403 to the token while the site believes it is activated, which is what a token replaced by a second activation looks like. At most once per install attempt. |
| Download the package | The address your install returns, which must be `https` (plain `http` only for a local host when the site's environment type is `local` or `development`) | A plain `GET` made with WordPress's safe HTTP client. It sends only the header `User-Agent: {product}; accessbay-sdk/{sdk version}`, not WordPress's default one, so no site URL goes to the package host. It stops reading after the signed size plus one byte and waits 5 minutes at most. The host may differ from your install, such as file storage. | Straight after the package request |

The update row WordPress shows carries only a placeholder for the package, the address of the SDK's own `packages`
route. It is never fetched. The SDK swaps it for the real, one-use address when the install starts, because that address
expires after 60 seconds.

The SDK checks the downloaded file against a signed SHA-256 and size before WordPress unpacks it, and discards it on any
mismatch. When the licence has lapsed, the update still shows but the package request and the download don't happen,
and automatic updates for that plugin stop.

### Release channels

A site gets stable releases unless its owner opts in to beta. The owner sets the constant `{PREFIX}_CHANNEL` to `beta`
(in `wp-config.php`), or the option `{prefix}_channel` to `beta`, where the prefix is the copy's `abs_…` prefix. The
constant wins over the option, and any other value means stable. Opted in, the SDK reads `latest-beta.json` instead of
`latest.json` (the channel is the document's name, not a query value) and adds `{"channel":"beta"}` to the package
request. Your AccessBay install answers with a beta release only when it offers the beta channel
(an add-on such as AccessBay Pro must add it through the `accessbay/delivery/release_channels` filter) and the product
has a live beta release; a request for `latest.json`, or any request sent to an install that does not offer beta,
always gets the newest stable release. A site on beta is also offered a stable release that is newer than the newest beta.

## Check it yourself

In this release, the SDK sends its requests from one file, `src/Http.php`: the licence calls and the package request
through `wp_remote_post()`, the release information through `wp_remote_get()` and the package download through
`wp_safe_remote_get()`. In the AccessBay plugin's own code, the one class that sends a request is
`WpPinnedTransport`, behind `SafeHttpClient`. A search for `wp_remote_` and `wp_safe_remote_` in the SDK's `src/` folder and
the plugin's `src/` folder finds those two places. The bundled job runner sends
one more request. To find it, search `vendor-prefixed/woocommerce/action-scheduler` for `wp_remote_post`.
