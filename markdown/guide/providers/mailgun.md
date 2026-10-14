---
title: "Mailgun"
description: "How to connect Mailgun to AccessBay - add and verify a domain, get an API key, fill in the form and set up delivery events."
---
Mailgun is a Sinch company. AccessBay shows this provider as **Mailgun (API)**.

## Before you start

1. In Mailgun, add your sending domain. A subdomain such as `mg.example.com` keeps your main domain's mail separate.
2. Add the DNS records Mailgun shows for the domain: an SPF record (`v=spf1 include:mailgun.org ~all`), a DKIM TXT
   record, a tracking CNAME pointing to `mailgun.org`, and MX records (`mxa.mailgun.org` and `mxb.mailgun.org`, priority
   10).
3. Wait until Mailgun marks the domain as verified. This can take up to 48 hours. An unverified domain has a limit of
   300 emails a day.

Mailgun keeps the US and EU regions apart. An account and its domain exist in one region only.

## Get the key

Mailgun has two kinds of key:

- **Domain sending key.** Created under Sending > Domains > your domain > Domain Settings > Sending API keys > **Add
  Sending Key**. It can only send, and Mailgun shows it once.
- **Account API key.** Found under Account Settings > API Keys.

A domain sending key is the narrowest choice. But **Test connection** in AccessBay reads the domain's record from
Mailgun, and a sending-only key may be refused for that call. If the test fails with an authentication error although
the key and region are right, use the account API key instead.

## Fill in AccessBay

Go to Settings > Email providers, select **Add provider** and pick **Mailgun (API)**.

| AccessBay field | Where the value comes from |
|---|---|
| Name | Any name you like. |
| From address | An address on the verified domain. |
| API key | The key from "Get the key". |
| Sending domain | The domain you verified in Mailgun, for example `mg.example.com`. |
| Region | **US** (`api.mailgun.net`) or **EU** (`api.eu.mailgun.net`). It must match the region of your Mailgun account. |
| Webhook signing key | Mailgun > Settings > API Security > HTTP webhook signing key. Leave empty if you skip webhooks. |

The signing key is the same for the whole account and is separate from your API keys.

**Test connection** checks the key and the sending domain. It sends no email.

## Delivery events (webhook)

1. In AccessBay, copy the **Webhook URL** from the **Webhooks** card.
2. In Mailgun, add an HTTP webhook for each of these events: delivered, permanent failure, temporary failure,
   complained and unsubscribed. Mailgun's documentation says webhooks are under Sending > Webhooks, and that you can set
   them for a domain or for the whole account. The menu may look different in your account.
3. Paste the URL for each event. Mailgun allows up to three URLs per event type, and needs HTTPS with a certificate
   from a public certificate authority.
4. Copy the HTTP webhook signing key into **Webhook signing key** in AccessBay and save.

## Common errors

- **Authentication errors.** The region is wrong, or the key is a sending key that the test can't use. Check the
  **Region** first.
- **The domain is not found.** The **Sending domain** must match the domain in Mailgun exactly.
- **Mailgun refuses the email.** The domain is not verified yet. Check the DNS records.

## Vendor documentation

- [Verify a domain](https://documentation.mailgun.com/docs/mailgun/user-manual/domains/domains-verify)
- [API authentication and key types](https://documentation.mailgun.com/docs/mailgun/api-reference/authentication)
- [Configuring webhooks](https://documentation.mailgun.com/docs/mailgun/user-manual/webhooks/configuring-webhooks)
- [Securing webhooks](https://documentation.mailgun.com/docs/mailgun/user-manual/webhooks/securing-webhooks)
