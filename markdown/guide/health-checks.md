---
title: "Health checks"
description: "What AccessBay checks about its own server, where to see the results, and how to point an uptime monitor at it."
---
AccessBay checks the things it depends on (its secret, the database, background jobs, email, the clock and more)
and switches off only the features a failing check affects, instead of failing silently or giving wrong answers.

## Where to see the results

- **Site Health:** in wp-admin, go to **Tools → Site Health**. Each AccessBay check is its own test, named as in
  the table below.
- **WP-CLI:** `wp accessbay health` runs every check now and prints a table. It exits with status 1 when any check is
  critical, so it works in scripts. `--cached` prints the stored results without running anything, and
  `--format=json` prints JSON.
- **REST:** `GET /wp-json/accessbay/v1/health` returns every result and the stored alerts, for users who can manage
  AccessBay. See the [API reference](/api/).

<!-- screenshot: Tools → Site Health with the AccessBay tests listed -->

## The checks

| Check | What it looks at | At worst |
|---|---|---|
| AccessBay secret and key ids | `ACCESSBAY_SECRET` is set and valid, and every stored record can be read | Critical |
| AccessBay proxy, client IP and TLS | HTTPS, and how visitor addresses are read behind a proxy or CDN | Warning |
| AccessBay background jobs | Scheduled jobs and queued emails run on time | Critical |
| AccessBay rate limiter storage | The rate limiter can count requests | Critical |
| AccessBay database schema, engine and topology | Tables complete and InnoDB, and every query reaching the same writable server | Critical |
| AccessBay customer portal page | A published page with the portal block is chosen under Settings → General ([the portal page](/guide/installation/#the-customer-portal-page)) | Warning |
| AccessBay email delivery | Recent email failures, and a daily test email if you set one up | Warning |
| AccessBay and the object cache | How AccessBay works with a persistent object cache | Info |
| AccessBay health checks are running | The other checks have run recently | Critical |
| AccessBay server clocks | PHP's clock and time zone against the database's clock | Warning |
| AccessBay PHP requirements | The PHP version and extensions from [Requirements](/guide/requirements/) | Critical |
| AccessBay and caches in front of the site | A page cache or CDN that keeps answers it was told not to keep | Warning |

A critical result switches off only what depends on it. For example, without the secret, live licences can't be
issued, and while the rate limiter can't count, the routes it protects answer `503`. Everything switches back on by
itself once the check passes again: a critical check is re-run every 10 minutes.

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

Two health settings exist today. A screen to change them is coming in a later release.

- `queue_warn_minutes`: how many minutes a due job or queued email may wait before the background-jobs check warns
  (1 to 59, default 10). It turns critical after 60 minutes.
- `mail_canary_enabled`: off by default. When on, a daily test email goes to `health_email_canary`, or to the site
  administrator's email when that is empty ([Email setup](/guide/email/)). An address alone does not turn it on.
