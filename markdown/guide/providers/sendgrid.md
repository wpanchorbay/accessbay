---
title: "Twilio SendGrid"
description: "How to connect Twilio SendGrid to AccessBay - verify a sender, create an API key, fill in the form and set up delivery events."
---
AccessBay shows this provider as **Twilio SendGrid (API)**. It sends through SendGrid's web API.

## Before you start

You need a SendGrid account and a verified sender. Use one of these:

- **Domain authentication** (best for production). In SendGrid, go to Settings > Sender Authentication > Domain
  Authentication and select **Get Started**. SendGrid shows DNS records to add at your DNS host: by default three CNAME
  records and one DMARC TXT record. With automated security turned off, it shows one MX and three TXT records
  instead. Add them, then verify in SendGrid.
- **Single sender**. Go to Marketing > Senders, add the address, then select the link SendGrid emails to that address.

The **From address** in AccessBay must belong to the verified domain or be the verified single sender.

## Get the key

1. In SendGrid, go to Settings > API Keys and select **Create API Key**.
2. Name it, for example "AccessBay".
3. Choose **Custom Access**, then set **Mail Send** to **Full Access**. Nothing else is needed.
4. Select **Create & View** and copy the key. SendGrid shows it once.

A key without the Mail Send permission fails the test.

## Fill in AccessBay

Go to Settings > Email providers, select **Add provider** and pick **Twilio SendGrid (API)**.

| AccessBay field | Where the value comes from |
|---|---|
| Name | Any name you like. |
| From address | A verified sender or an address on the authenticated domain. |
| From name | Optional. |
| API key | The key from step 4 above. |
| Region | **Global** (`api.sendgrid.com`) for most accounts. **EU** (`api.eu.sendgrid.com`) only if you use an EU subuser. EU sending needs the Pro plan or above. The field is required. |
| Webhook verification key | The public key from the signed event webhook (see below). Leave empty if you skip webhooks. |

**Test connection** checks that the key can send mail. It sends no email.

## Delivery events (webhook)

1. In AccessBay, copy the **Webhook URL** from the **Webhooks** card.
2. In SendGrid, go to Settings > Mail Settings > Webhook Settings > Event Webhooks and create a webhook with that URL.
3. Tick the events: delivered, bounce, dropped, spam report and unsubscribe. AccessBay counts a dropped event as a hard
   bounce.
4. Turn on **Enable Signed Event Webhook** and save.
5. The key pair exists only after you save. Reopen the webhook (cog icon, then **Edit**) and copy the public
   verification key.
6. Paste it into **Webhook verification key** in AccessBay and save.

Don't use SendGrid's test button before step 5 is done. Until the key is in AccessBay, the signature can't be checked.

## Common errors

- **The test fails with a permission message.** The key lacks Mail Send. Create a new key as in "Get the key".
- **The test fails on an EU account.** Check the **Region**. The EU host needs an EU subuser, and the Pro plan or above.
- **SendGrid refuses the email.** The From address is not verified. Verify the sender or domain.
- **No delivery events arrive.** Check that the signed event webhook is on, and that the verification key in AccessBay
  is the public key of the same webhook.

## Vendor documentation

- [API keys](https://www.twilio.com/docs/sendgrid/ui/account-and-settings/api-keys)
- [Domain authentication](https://www.twilio.com/docs/sendgrid/ui/account-and-settings/how-to-set-up-domain-authentication)
- [Single sender verification](https://www.twilio.com/docs/sendgrid/ui/sending-email/senders)
- [Signed event webhook](https://www.twilio.com/docs/sendgrid/for-developers/tracking-events/getting-started-event-webhook-security-features)
