---
title: "The secret key"
description: "Set ACCESSBAY_SECRET before going live, what happens without it, and how to change it safely."
---
AccessBay protects the licence keys, tokens and service credentials it stores with keys derived from one site secret,
`ACCESSBAY_SECRET`. The secret lives in your server configuration, never in the database, so a database backup or leak
alone can't open what it protects. It does not depend on WordPress's salts: changing those leaves AccessBay alone.

## Set the secret

1. Generate 32 random bytes:

   ```sh
   openssl rand -base64 32
   ```

2. Add the result to `wp-config.php`, above the line that says "That's all, stop editing!":

   ```php
   define( 'ACCESSBAY_SECRET', 'paste-the-value-here' );
   ```

   Or set an environment variable named `ACCESSBAY_SECRET` for PHP. When both exist, the constant wins.

3. If the site has already run AccessBay without a secret, move its records to the new key:

   ```sh
   wp accessbay keys rotate
   ```

The value must decode to exactly 32 bytes: base64, base64url or 64 hexadecimal characters. Anything else is refused
and Site Health reports it as critical.

<!-- screenshot: Site Health showing the "AccessBay secret and key ids" test as passed -->

Keep a copy of the secret somewhere safe and separate from your database backups. Without it, the records it protects
can't be read.

## Without the secret

- **In test mode**, AccessBay generates a key and stores it in the database, so you can try things out. Site Health
  warns that a generated test-mode key is in use.
- **In live mode**, AccessBay refuses to issue live licences or save live credentials until the secret is defined.
  Site Health reports it as critical, and requests that need it answer `503` with the error code
  `accessbay_temporarily_unavailable`, which means "try again later", never "your key is invalid".

## Change the secret

1. Move the current value into `ACCESSBAY_SECRET_PREVIOUS`, so existing records stay readable. Several old values can
   be listed, separated by commas.
2. Set a new `ACCESSBAY_SECRET`.
3. Run `wp accessbay keys rotate`. It moves every record to the new key and ends with "Every record is under key id
   …". If it warns that records are still under an older key id, run it again. Once it succeeds, you can remove the
   old value from `ACCESSBAY_SECRET_PREVIOUS`.

If records were protected by a secret that is no longer configured, Site Health reports it as critical and those
records answer `503` until you add that secret to `ACCESSBAY_SECRET_PREVIOUS`.

## The public signing key

AccessBay also signs some of its answers so that client software can check they really came from your site.
`wp accessbay keys show` prints the public half of that signing key. It doesn't change when you change the secret.
