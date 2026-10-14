---
title: "Postmark"
description: "How to connect Postmark to AccessBay - confirm a sender, copy the server API token, fill in the form and set up delivery events."
---
## Before you start

Postmark refuses an email from an address it hasn't verified. It answers with an HTTP 422 error. Do one of these in
Postmark:

- Confirm a **Sender Signature** for the From address.
- Verify the whole domain. This needs DKIM records. Postmark also recommends a custom Return-Path, which is a CNAME
  record pointing to `pm.mtasv.net`.

A new Postmark account starts with limits, such as 100 test emails a month on the free plan. Check Postmark's pages for
what applies to your account.

## Get the key

1. In Postmark, open your **Server** (Servers > your server).
2. Open the **API Tokens** tab.
3. Copy the **Server API token**.

Don't use the Account API token. It can only manage servers, not send mail.

## Fill in AccessBay

Go to Settings > Email providers, select **Add provider** and pick **Postmark**.

| AccessBay field | Where the value comes from |
|---|---|
| Name | Any name you like. |
| From address | A confirmed Sender Signature or an address on the verified domain. |
| Server API token | The token from "Get the key". |
| Message stream | Leave empty for the default transactional stream (`outbound`). Enter a stream's name only if you use another one. |
| Webhook username | A user name you choose. Leave empty if you skip webhooks. |
| Webhook password | A password you choose. |

**Test connection** checks the token. It sends no email.

## Delivery events (webhook)

Postmark doesn't sign webhooks. It uses basic authentication, with the user name and password inside the URL.

1. In AccessBay, copy the **Webhook URL** from the **Webhooks** card.
2. Choose a user name and a password. Enter them in **Webhook username** and **Webhook password** in AccessBay and
   save.
3. In Postmark, open your server's webhook settings and add a webhook. Use the AccessBay URL with the pair in front
   of the host: `https://user:password@your-site.example/...`.
4. Tick **Delivery**, **Bounce** and **Spam complaint**.
5. Postmark's **Verify webhook** button sends a test for each event. Use it to check the setup.

Postmark's menu names and per-stream webhook options may differ from this description. See Postmark's webhook guide
below.

## Common errors

- **HTTP 422 from Postmark.** The From address is not a confirmed sender or on a verified domain.
- **The test fails with an authentication error.** You may have copied the Account API token. Use the Server API token.
- **No delivery events arrive.** Check that the user name and password in the URL match the two fields in AccessBay.

## Vendor documentation

- [Account and server API tokens](https://postmarkapp.com/support/article/1008-what-are-the-account-and-server-api-tokens)
- [Getting started](https://postmarkapp.com/support/article/1002-getting-started-with-postmark)
- [Sender Signatures](https://postmarkapp.com/developer/user-guide/managing-your-account/managing-sender-signatures)
- [Webhooks overview](https://postmarkapp.com/developer/webhooks/webhooks-overview)
