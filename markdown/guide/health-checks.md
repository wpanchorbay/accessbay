---
title: "Health checks"
description: "What AccessBay checks about its own server, how to read the Health screen, what each message means and how to fix it, and how to point an uptime monitor at it."
---
AccessBay checks the things it depends on (its secret, the database, background jobs, email, the clock, file storage
and more) and switches off only the features a failing check affects, instead of failing silently or giving wrong
answers.

## Where to see the results

- **The Health screen:** go to AccessBay > Health. This is the main place. It explains each result and offers the
  steps to fix it.
- **Site Health:** in wp-admin, go to **Tools > Site Health**. Each AccessBay check is its own test, named as in the
  table below.
- **WP-CLI:** `wp accessbay health` runs every check now and prints a table. It exits with status 1 when any check is
  critical, so it works in scripts. `--cached` prints the stored results without running anything, and
  `--format=json` prints JSON.
- **REST:** `GET /wp-json/accessbay/v1/health` returns every result and the stored alerts, for users who can manage
  AccessBay. See the [API reference](/api/).

## Reading the Health screen

The screen has one card called **Checks**, headed "What AccessBay verifies about this site, and when it last did."

### The Overall card

The first card, **Overall**, has a status label and a summary.

- When every check passes, it says "All 16 checks pass. Last ran" and the time. The number is how many checks there are.
- Otherwise it counts what needs attention, for example "2 warnings, 1 notice out of 16 checks need attention:", and lists
  each one with its status, its name and its summary. Select a name to jump to its card.

### Statuses

| Status | Meaning |
|---|---|
| Good | The check passed. |
| Notice | Information. Nothing is wrong, or the check could not prove either way. |
| Warning | Something should be fixed, but AccessBay still works. |
| Critical | A feature that depends on this check is switched off until it is fixed. |
| Not run yet | The check has not produced a result. |

### A check's card

Every card shows the check's name, a one-line summary, its status and a message in plain words. Every result has a
message, including a good one.

When a result is a warning or critical, the card also has **What you can do**: at least one step. A step is one of two
things.

- A **link button** that opens the WordPress or AccessBay page where you fix it, such as the plugins list or the general
  settings.
- A **command or setting** in a grey box with a **Copy** button. Select **Copy** and AccessBay shows "Copied". If your
  browser refuses, it says "Could not copy. Select the text and copy it by hand." Paste WP-CLI commands into a terminal
  on your server, and paste `wp-config.php` lines into that file. Where a line needs your own value, the text is a
  placeholder such as `paste-your-long-random-secret-here`. AccessBay never puts a real secret into a step.

A notice can have no step. The card also shows the last error if there was one, when the check **Last run**, and when it
is **Next due**.

<!-- screenshot: the Health screen with the Overall card and one warning card -->

## The checks

| Check | What it looks at | At worst |
|---|---|---|
| AccessBay secret and key ids | `ACCESSBAY_SECRET` is set and valid, and every stored record can be read | Critical |
| AccessBay proxy, client IP and TLS | HTTPS, and how visitor addresses are read behind a proxy or CDN | Warning |
| AccessBay background jobs | Scheduled jobs and queued emails run on time | Critical |
| AccessBay rate limiter storage | The rate limiter can count requests | Critical |
| AccessBay database schema, engine and topology | Tables complete and InnoDB, and every query reaching the same writable server | Critical |
| AccessBay customer portal page | A published page with the portal block is chosen under Settings > General | Warning |
| AccessBay email delivery | Providers working, recent failures, credits, webhooks and the daily test email | Warning |
| AccessBay and the object cache | How AccessBay works with a persistent object cache | Notice |
| AccessBay health checks are running | The other checks have run recently | Critical |
| AccessBay server clocks | PHP's clock and time zone against the database's clock | Warning |
| AccessBay PHP requirements | The PHP version and extensions from [Requirements](/guide/requirements/) | Critical |
| AccessBay and caches in front of the site | A page cache or CDN that keeps answers it was told not to keep | Warning |
| AccessBay product entitlements | Every software product grants an entitlement | Critical |
| AccessBay release signing | Signatures verify against the published key list | Critical |
| AccessBay file storage | Release files can be written, found and removed | Critical |
| AccessBay file storage is not web-reachable | Nobody can download stored files without a licence | Critical |

A critical result switches off only what depends on it. For example, without the secret, live licences can't be
issued, and while the rate limiter can't count, the routes it protects answer `503`. Everything switches back on by
itself once the check passes again: a critical check is re-run every 10 minutes.

## What each message means

### Secret and key ids

Checks that AccessBay has a secret and that every record it stored can be opened with it. See
[The secret key](/guide/secret-key/) for how the secret works.

- **"ACCESSBAY_SECRET is malformed".** The secret AccessBay was given is not in the format it expects. Live issuance and
  live credential saves are refused. The steps offer a `define( 'ACCESSBAY_SECRET', ... )` line to correct it in
  `wp-config.php`, or restoring the key file.
- **"AccessBay is creating its test-mode key".** Reload the screen. If it stays, run `wp accessbay keys init`.
- **"The secret key file cannot be used".** The web server user can't read the file, or it is unusable. Give the web
  server user read access without making the file readable by everyone, or restore it from a backup with
  `wp accessbay keys restore`. The step `ls -l ../accessbay-private/ wp-content/accessbay-private/` shows who owns it.
- **"The secret key file is missing".** Usually the database was copied to another host, or its name or table prefix
  changed. Run `wp accessbay keys restore`, or restore the file from a backup.
- **"AccessBay is using a generated test-mode key".** Fine for testing. Before going live, run `wp accessbay keys init`,
  or define `ACCESSBAY_SECRET` in `wp-config.php`, then run `wp accessbay keys rotate`.
- **"The secret key file is waiting for its exposure check".** The file sits inside `wp-content` and AccessBay checks
  every few hours that the web can't reach it. To skip the wait, define `ACCESSBAY_SECRET` in your environment or server
  config, or move the file above the web root. `wp accessbay health` runs the check again now.
- **"AccessBay has no secret key".** On a new site run `wp accessbay keys init`. On a restored site run
  `wp accessbay keys restore`. Or define `ACCESSBAY_SECRET`.
- **"The secret equals the stored test-mode key".** Run `wp accessbay keys rotate --generate`, or set a new
  `ACCESSBAY_SECRET` and run `wp accessbay keys rotate`.
- **"The secret was exposed publicly".** Treat it as leaked. Run `wp accessbay keys rotate --generate`, then
  `wp accessbay keys rotate`.
- **"Some records are under a key no secret can open".** AccessBay answers `503` for them. Restore the key file, or list
  the old secret in `wp-config.php` with `define( 'ACCESSBAY_SECRET_PREVIOUS', 'paste-the-old-secret-here' );` and run
  `wp accessbay keys rotate`.
- **"Some records are still under an older key".** Run `wp accessbay keys rotate`.
- **"The generated test-mode key is still stored".** Run `wp accessbay keys rotate`.
- **"The secret key file needs attention".** Follow the Site Health link, then run `wp accessbay health`.
- **Good: "AccessBay has its own secret".** It names where the secret comes from and reminds you to keep a copy of the
  recovery key (`wp accessbay keys recovery`).

### Proxy, client IP and TLS

- **Good: "Proxy and TLS settings look right".**
- **Warning: "Proxy or TLS settings need attention".** It lists what it found. One finding is that the site address is
  `https` but PHP does not see the request as HTTPS, which means the server or proxy is not passing HTTPS through to
  WordPress. Others come from the request log: a CDN or proxy you did not trust, a trusted proxy that is never used, or
  forwarding headers from an untrusted source. It adds that rate limits currently count per proxy. Open WordPress Site
  Health, and run `wp accessbay proxies show` to list the proxies AccessBay trusts. The **Trusted proxies** and **Behind
  Cloudflare** settings are under Settings > Network.
- **Notice: "Proxy diagnostics are not available yet"** (or "TLS looks right; proxy diagnostics are not available
  yet"). The request-log diagnostics are not available yet. Nothing is wrong. The step shows the trusted proxies.

### Background jobs

- **Good: "AccessBay jobs are running on time".**
- **"AccessBay jobs are N minutes behind".** A warning once the delay passes the **Queue warning** setting (10 minutes by
  default), and critical at 60. Run `wp accessbay jobs run` to clear the backlog. For a lasting fix run WP-Cron from a
  system cron, with a line like
  `* * * * * cd /path/to/wordpress && wp cron event run --due-now >/dev/null 2>&1`.
- **"N background items gave up after their retries".** Open AccessBay to see them. `wp accessbay jobs list` lists them.

### Rate limiter storage

- **Good: "The rate limiter writes durably".**
- **"The rate limiter cannot write".** Limited requests answer `503`. Open Site Health, and run `wp db check`.
- **"The rate limiter does not count".** A database setting (the `CLIENT_FOUND_ROWS` flag) or a `db.php` drop-in stops it
  counting. Look at `ls -l wp-content/db.php`, then run `wp accessbay health`.
- **"Rate limiter visibility is not checked".** Add `define( 'ACCESSBAY_DB_SECOND_SESSION', true );` to
  `wp-config.php` to let AccessBay prove that a second database connection sees its writes.
- **"Rate limiter visibility is unverified".** The database refused a second connection. Ask your host about the
  per-user connection limit.
- **Critical: "Another database session does not see the rate limiter's writes".** The database is not durable or is
  split between servers, so limits could be bypassed. Unauthenticated AccessBay routes answer `503` until this passes.
  Point the site at a single database server for writes, or ask your host to, then run `wp accessbay health`.

### Database schema, engine and topology

- **Good: "The database schema and topology are sound".**
- **Critical: "The database cannot guarantee AccessBay's locks".** It lists what is wrong: a replica-routing `db.php`,
  tables that are missing or not InnoDB, missing unique keys, or a migration that did not finish. Issuing licences,
  activation, fulfilment and token redemption answer `503` until it is fixed. Validating and deactivating keep working.
  Open the plugins list to look for a database plugin, run
  `wp db query "SHOW TABLE STATUS LIKE '%accessbay%'"`, then `wp accessbay health`.

### Customer portal page

- **Good: "The customer portal page is set".**
- **Warning: "No customer portal page is set" or "The customer portal page can't be used".** The page was deleted or
  trashed, is not published, or lacks the portal block or the `[accessbay_portal]` shortcode. Choose a page under
  Settings > General > Customer portal page, or add a new page. The steps are **Open general settings** and **Add a
  new page**. See [the portal page](/guide/installation/#the-customer-portal-page).

### Email delivery

- **Good: "Email is being delivered".**
- **Warning: "Email delivery needs attention".** It lists what it found: `wp_mail` is the only provider, a provider is
  paused after repeated failures, a provider's last test failed, credits ran out or will in a number of days, a provider
  with a webhook reported nothing for 7 days, emails failed every retry in the last 24 hours, the primary provider
  failed in the last 24 hours, the site's own mail failed in the last 24 hours, or a provider refused the daily test
  email. It adds that licence keys and downloads stay on screen after purchase, so customers are not blocked. The step
  **Open email providers** takes you to the fix. See [Email setup](/guide/email/).
- **Notice: "The daily email check is on hold".** This site is not a production site, so the daily test email is held.
  Add the recipient to the staging email allowlist to try it.
- **Notice: "No email has failed recently".** The daily test email is off, so the check judges from real sends only.

### Object cache

A notice only: "A persistent object cache is in use" or "No persistent object cache is in use". AccessBay works the same
either way.

### Health checks are running

- **Good: "Health checks are running on time".**
- **Warning: "Some health checks are overdue" (with the names) or "Health checks have not run on traffic in the last 24
  hours".**
- **Critical: "Health checks have not run for over 3 days".**

Run `wp accessbay health` once, and set up a system cron as described under "When checks run" below.

### Server clocks

- **Good: "Server clocks agree".**
- **Warning: "Server clocks disagree".** The database clock and PHP's clock differ by more than 60 seconds, or PHP's
  default time zone is not UTC because a plugin or the server changed it. Times AccessBay records are still UTC. The
  steps open WordPress's general settings (time zone) and show `date -u`, the server time. Ask your host to sync the
  clocks with NTP.

### PHP requirements

- **Good: "This server has everything AccessBay needs".**
- **Critical: "This server is missing something AccessBay needs".** Typically curl with SSL or openssl. Live mode and
  outbound requests are refused until it is fixed.
- **Warning: "The PHP zip extension is missing".** Release uploads are refused. A warning "Some query filters do not apply
  to AccessBay" means something is changing the database queries.
- **Notice: "AccessBay uses WordPress's sodium_compat".** The PHP sodium extension is not loaded, so cryptography runs
  in PHP code: correct, but slower. You can ask your host to enable the extension. Nothing needs to be done otherwise.

Open Site Health's debug information, and run `php -m` to list the extensions.

### Caches in front of the site

- **Good: "No cache in front of the site ignores AccessBay's headers".**
- **Warning: "A cache in front of the site ignores AccessBay's no-store headers".** Exclude `/wp-json/accessbay/*` from
  the cache, and make uptime monitors `POST` the health ping. Open the plugins list to look for a caching plugin, then
  run `wp accessbay health`.
- **Notice: "Edge cache check unverified".** The site's name points at a private address or the test request failed, so
  AccessBay could not tell.

### Product entitlements

- **Good: "Every AccessBay software product grants an entitlement".**
- **Critical: "AccessBay software products grant no entitlement".** It is a warning if only draft products are affected.
  Licences sold for them can't be activated on a site. Products created in AccessBay get an entitlement
  automatically, so these were written another way. Archive each one and create it again under a new slug. The step
  **Open products** takes you there.

### Release signing

- **Good: "AccessBay signatures verify".**
- **Warning: "AccessBay has no published signing key list".** Run `wp accessbay keys show`, then
  `wp accessbay keys publish`.
- **Critical: "AccessBay signatures do not verify".** Compare the signing key with the published list, and publish the
  list again with `wp accessbay keys publish`. Publishing is refused until it verifies.
- **Warning: "AccessBay can't sign yet".** Neither `ACCESSBAY_SECRET` nor the secret key file is available. Run
  `wp accessbay keys init`, or define your own secret.

### File storage

- **Good: "AccessBay file storage works".**
- **Critical: "AccessBay file storage is not working".** Downloads stay off. Make sure the storage folder exists and the
  web server user may write to it. `ls -ld ../accessbay-storage accessbay-storage` shows who owns it. Then run
  `wp accessbay health`.

### File storage is not web-reachable

- **Good: "AccessBay file storage is not web-reachable".**
- **Critical: "AccessBay file storage can't be used".** The folder can't be created, or it is inside the plugins folder.
- **Critical: "AccessBay file storage is web-reachable".** A test file was served over HTTP, so paid files could be
  downloaded without a licence. Downloads stay off. Add a line like
  `define( 'ACCESSBAY_STORAGE_DIR', '/path/outside/the/web/root/accessbay-files' );` to `wp-config.php` with a folder your
  web server can write to but does not serve.
- **Warning: "File storage exposure unverified".** AccessBay could not run the test. If you know your web server does not
  serve the folder, tick "I have checked that this folder cannot be opened from the web" on the card and select
  **Confirm folder is private**. Or move the folder as above.

## When checks run

Checks run in the background on normal site traffic, on WP-Cron, and from WP-CLI. A request runs at most one check
that is due, so visitors don't notice. On a quiet site, or one that sets `DISABLE_WP_CRON`, run `wp-cron.php` or
`wp accessbay health` from a system cron every few minutes. Otherwise results grow old: the "health checks are
running" check warns, and turns critical (making the ping answer `503`) when a result is more than three days old.

The background-jobs and checks-running checks also email the site's admin address, at most once a day each, because
the queue that would normally send an alert is the thing that failed.

## Health ping for an uptime monitor

Point an external monitor at the health ping:

```sh
curl -X POST https://example.com/wp-json/accessbay/v1/health/ping
```

It answers `200` with `{"status":"ok","age":120}` while nothing is critical, and `503` with
`{"status":"critical","age":120}` when something is. `age` is the number of seconds since the newest check result
(`null` before any check has run). Use `POST`: a monitor that can only send `GET` can use the same address, but a cache
in front of the site may answer a `GET` from its copy.

The ping is limited to 60 requests a minute per client and 6000 a minute for the whole site. A `429`, or a `503` with
the error code `accessbay_temporarily_unavailable`, means the site is being flooded or its rate limiter is down, not
that a check failed. Alert on it separately from a `503` that says `"status":"critical"`.

## Settings

Two settings change how the checks behave. Both are on the Settings screen ([Plugin settings](/guide/plugin-settings/)).

- **Queue warning** (Settings > General): how many minutes a due job or queued email may wait before the
  background-jobs check warns (1 to 59, default 10). It turns critical after 60 minutes.
- **Send a daily test email** (Settings > Email settings): off by default. When on, a daily test email goes to **Daily
  test email recipient**, or to the site administrator's email when that is empty ([Email setup](/guide/email/)). An
  address alone does not turn it on.
