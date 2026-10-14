---
title: "ZeptoMail"
description: "How to connect ZeptoMail (Zoho) to AccessBay - verify a domain, copy the Send Mail token and fill in the form. Delivery events are not available."
---
ZeptoMail is Zoho's transactional email service. Zoho's current documentation calls it Zoho CPaaS, and AccessBay
shows it as **ZeptoMail (Zoho CPaaS)**.

## Before you start

In ZeptoMail, add and verify your sending domain on the mail agent you will use. The **From address** must be on that
domain. See ZeptoMail's pricing page for the free tier.

## Get the key

1. In ZeptoMail, go to Mail Agents and open your agent.
2. Open the **SMTP/API** tab, then **API**.
3. Copy the **Send Mail token**, or generate one.

The token may start with `Zoho-enczapikey`. You can include that prefix or leave it out.

## Fill in AccessBay

Go to Settings > Email providers, select **Add provider** and pick **ZeptoMail (Zoho CPaaS)**.

| AccessBay field | Where the value comes from |
|---|---|
| Name | Any name you like. |
| From address | An address on the verified domain. |
| Send Mail token | The token from "Get the key". |
| API host | Leave empty to use `cpaas.zoho.com`. Enter `cpaas.zoho.eu` for the EU data centre. AccessBay also accepts `api.zeptomail.com` and `api.zeptomail.eu`. Use the host that matches your Zoho account's region. |

ZeptoMail has no way to check a token without sending. **Test connection** sends one test email to your own admin
address.

## Delivery events (webhook)

Not available. AccessBay 1.0 has no ZeptoMail delivery events and shows no webhook URL for this provider. An email
sent through ZeptoMail stays at "Sent". For delivered and bounced emails in AccessBay, use another provider.

## Common errors

- **The test fails with an authentication error.** The token is for a different mail agent, or the API host is for
  another region.
- **ZeptoMail refuses the email.** The From address isn't on the verified domain.
- **The API host is refused.** AccessBay accepts only the four hosts listed in the table.

## Vendor documentation

- [ZeptoMail help](https://www.zoho.com/zeptomail/help/)
