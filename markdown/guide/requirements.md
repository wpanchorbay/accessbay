---
title: "Requirements"
description: "The WordPress, PHP and database versions AccessBay needs, and the PHP extensions it checks for."
---
## Software

| What | Minimum |
|---|---|
| WordPress | 6.8 |
| PHP | 8.3 |
| Database | MySQL 8.0, or MariaDB 10.5, through PHP's `mysqli` extension |

## PHP extensions

AccessBay checks these when you activate it. If one is missing, activation stops with a message that names it, before
anything is written to your database.

- **curl**, with SSL support
- **openssl**
- **mysqli**
- **sodium**, or the copy of sodium_compat that WordPress itself bundles. Without the extension AccessBay still works
  and Site Health notes it.
- A working `random_bytes()`, which every supported PHP version has unless the host has broken it.

After activation, AccessBay keeps checking. If curl or openssl disappears later (after a PHP upgrade, for example),
live mode and outgoing connections switch off until it is back, and Site Health says why.

## Database

- AccessBay's tables use the **InnoDB** engine. A table converted to another engine is reported as critical.
- AccessBay needs every query to reach the **same writable database server**. A set-up that sends some reads to a
  replica (a "split" database drop-in, for example) breaks the locking AccessBay relies on; the health checks detect it
  and switch off the features that depend on it.
- `MYSQL_CLIENT_FLAGS` must not include `MYSQLI_CLIENT_FOUND_ROWS`. With it, the database reports matched rows instead of
  changed rows, and AccessBay's rate limiter could never count correctly, so activation refuses it.

## Scheduled tasks

AccessBay runs its background jobs and health checks on WP-Cron and on normal site traffic. If your site sets
`DISABLE_WP_CRON`, run `wp-cron.php` (or `wp accessbay health`) from a system cron every few minutes. See
[Health checks](/guide/health-checks/).
