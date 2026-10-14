---
title: "SMTP2GO"
description: "How to connect SMTP2GO to AccessBay - verify a sender, create an API key, fill in the form and set up delivery events."
---
AccessBay shows this provider as **SMTP2GO (API)**. It sends through SMTP2GO's web API. To use SMTP2GO's SMTP server
instead, use the [SMTP](/guide/providers/smtp/) provider.

## Before you start

In SMTP2GO, verify a sender address or a whole sending domain. The **From address** in AccessBay must be a verified
sender. SMTP2GO shows the DNS records for a domain in its dashboard. See SMTP2GO's documentation for the free plan's
limits.

## Get the key

1. In SMTP2GO, go to Sending > **API Keys** and add a key.
2. Give the key permission for the email send endpoint (`/email/send`). Leave the other permissions off. The exact
   label in the dashboard may differ.
3. Copy the key. SMTP2GO keys start with `api-`.

## Fill in AccessBay

Go to Settings > Email providers, select **Add provider** and pick **SMTP2GO (API)**.

| AccessBay field | Where the value comes from |
|---|---|
| Name | Any name you like. |
| From address | A verified sender or an address on the verified domain. |
| API key | The key from "Get the key". |
| Region | **Automatic** unless your account is bound to a data region. Then pick United States, Europe or Australia to match it. |
| Webhook username | A user name you choose. Leave empty if you skip webhooks. |
| Webhook password | A password you choose. |
| Webhook bearer token | Optional. A long random value (32 characters or more) that SMTP2GO sends as an `Authorization: Bearer` header, instead of the user name and password. |

SMTP2GO has no way to check a key without sending. **Test connection** sends one test email to your own admin address.
That proves the key, the region and the verified sender.

## Delivery events (webhook)

SMTP2GO doesn't sign webhooks. It can send a basic or bearer authorization header, or a user name and password in the
URL. AccessBay accepts either the user name and password pair or the bearer token, so you can use whichever SMTP2GO
sends. The webhook URL is public, so make the password or token long and random.

1. In AccessBay, copy the **Webhook URL** from the **Webhooks** card.
2. Choose a user name and a password. Enter them in **Webhook username** and **Webhook password** and save.
3. In SMTP2GO, go to Settings > Webhooks > **Manage Webhooks** > **Add Webhook**.
4. Enter the URL with the pair in front of the host: `https://user:password@your-site.example/...`.
5. Choose the users (API keys) it applies to, set the output to **JSON**, and tick the email events.
6. Fill in the **Email headers** option so the message id comes back with each event. Without it, AccessBay can't
   match an event to its email.
7. Save, then use **Test this webhook**.

AccessBay reports these events: delivered, hard bounce, soft bounce, spam, unsubscribe and reject.

## Common errors

- **The test fails.** Check the API key, the region and that the From address is a verified sender.
- **The key is refused.** The key may lack permission for the send endpoint.
- **A wrong region gives authentication errors.** Switch **Region** to **Automatic**, or to the one your account uses.
- **Events arrive but don't match an email.** The custom headers are not ticked in the webhook.

## Vendor documentation

- [Getting started with the API](https://developers.smtp2go.com/docs/getting-started)
- [Set up a webhook](https://developers.smtp2go.com/docs/setup-a-webhook)
