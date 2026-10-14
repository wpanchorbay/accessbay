---
title: "Brevo"
description: "How to connect Brevo (formerly Sendinblue) to AccessBay - authenticate a domain, create an API key, fill in the form and set up delivery events."
---
Brevo was called Sendinblue before.

## Before you start

In Brevo, authenticate your sending domain. Brevo asks for these DNS records:

- a TXT record with the Brevo code,
- DKIM, as one TXT record or two CNAME records,
- a DMARC TXT record.

SPF and MX records are not required for this. The free plan allows up to 300 emails a day once Brevo has approved your
account. See [Brevo's pricing](https://www.brevo.com/pricing/).

## Get the key

1. In Brevo, go to Settings > SMTP & API > **API keys**.
2. Select **Generate a new API key**, name it, and copy it. Brevo shows it once.

Brevo may also limit which IP addresses can use a key. If the test fails with an "unrecognised IP" message, look at
Brevo's security settings and allow your server's address.

## Fill in AccessBay

Go to Settings > Email providers, select **Add provider** and pick **Brevo**.

| AccessBay field | Where the value comes from |
|---|---|
| Name | Any name you like. |
| From address | An address on the authenticated domain, or a verified sender. |
| API key | The key from "Get the key". |
| Webhook bearer token | A token you make up, and also enter as the webhook's bearer authentication in Brevo. Leave empty if you skip webhooks. |

**Test connection** checks the key against your Brevo account. It sends no email.

## Delivery events (webhook)

Brevo sends events such as delivered, soft bounce, hard bounce, spam, blocked, invalid, deferred and error.

1. In AccessBay, copy the **Webhook URL** from the **Webhooks** card.
2. Choose a long random token. Enter it in **Webhook bearer token** in AccessBay and save.
3. In Brevo, add a transactional webhook with that URL. In Brevo's menu this may be under Transactional > Settings >
   Webhook, but check Brevo's documentation for the current location.
4. Set the webhook's authentication to bearer, with the same token. Brevo's documentation describes bearer
   authentication for its API (`POST /v3/webhooks`). If the dashboard has no field for it, create the webhook through
   the API.
5. Tick the events you want.

## Common errors

- **"Unrecognised IP" in the test.** The key is limited to certain IP addresses. Allow your server's address in Brevo.
- **Brevo refuses the email.** The sender or domain isn't verified, or the account isn't approved yet.
- **No delivery events arrive.** The bearer token in Brevo must be exactly the one in AccessBay.

## Vendor documentation

- [Authenticate your domain](https://help.brevo.com/hc/en-us/articles/12163873383186-Authenticate-your-domain-with-Brevo-Brevo-code-DKIM-DMARC)
- [API key authentication](https://developers.brevo.com/docs/api-key-authentication)
- [Transactional webhooks](https://developers.brevo.com/docs/transactional-webhooks)
- [Webhook authentication](https://developers.brevo.com/docs/username-and-password-authentication)
