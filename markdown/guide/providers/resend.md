---
title: "Resend"
description: "How to connect Resend to AccessBay - verify a domain, create a sending-only API key, fill in the form and set up delivery events."
---
## Before you start

In Resend, add and verify your sending domain. Resend shows the DNS records to add at your DNS host. Resend refuses an
email whose From address is on a domain that isn't verified.

The free plan allows 100 emails a day, 3,000 a month and 3 domains. Check [Resend's pricing](https://resend.com/pricing)
for current limits.

## Get the key

1. In Resend, go to **API Keys** and select **Create API Key**.
2. Name it, for example "AccessBay".
3. Set the permission to **Sending access**. If you like, limit it to your one domain.
4. Copy the key. Resend shows it once.

AccessBay accepts a key that can only send.

## Fill in AccessBay

Go to Settings > Email providers, select **Add provider** and pick **Resend**.

| AccessBay field | Where the value comes from |
|---|---|
| Name | Any name you like. |
| From address | An address on the verified domain. |
| API key | The key from "Get the key". |
| Webhook signing secret | The secret that starts with `whsec_`, shown on the webhook's page in Resend. Leave empty if you skip webhooks. |

**Test connection** checks the key. It sends no email.

## Delivery events (webhook)

1. In AccessBay, copy the **Webhook URL** from the **Webhooks** card.
2. In Resend, go to **Webhooks** and select **Add Webhook**. Paste the URL.
3. Tick the events: `email.delivered`, `email.bounced`, `email.complained`, `email.delivery_delayed`, `email.failed`
   and `email.suppressed`.
4. Save, open the webhook and copy its signing secret (it starts with `whsec_`).
5. Paste it into **Webhook signing secret** in AccessBay and save.

## Common errors

- **The test fails with an authentication error.** The key is wrong or was deleted. Create a new one.
- **Resend refuses the email.** The From address is on a domain that isn't verified yet. While a domain is not
  verified, Resend may allow test emails only to your own address.
- **No delivery events arrive.** Check that the secret in AccessBay is the one from the same webhook.

## Vendor documentation

- [Domains](https://resend.com/docs/dashboard/domains/introduction)
- [Create an API key](https://resend.com/docs/api-reference/api-keys/create-api-key)
- [Event types](https://resend.com/docs/dashboard/webhooks/event-types)
- [Verify webhook requests](https://resend.com/docs/dashboard/webhooks/verify-webhooks-requests)
