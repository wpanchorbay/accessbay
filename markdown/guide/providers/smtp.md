---
title: "SMTP"
description: "How to connect any SMTP server to AccessBay - Google Workspace and Gmail, Microsoft 365, or your host's mailbox."
---
Use the SMTP provider for any mail server that takes mail over SMTP. For SMTP2GO, Brevo and the other services in this
guide, their own provider type is better, because it can report delivery.

## Before you start

You need a mailbox on the server, with its address and password. The **From address** should be that mailbox's
address, or one the server allows you to send as.

## Fill in AccessBay

Go to Settings > Email providers, select **Add provider** and pick **SMTP**.

| AccessBay field | Where the value comes from |
|---|---|
| Name | Any name you like. |
| From address | The mailbox address. |
| SMTP host | The server name from the mail service or host. |
| Port | 587 for STARTTLS, 465 for SSL. |
| Encryption | **STARTTLS** with port 587, **SSL/TLS** with port 465. |
| Username | Usually the full mailbox address. |
| Password | The mailbox password, or an app password (see below). |
| Allow a local server | Leave off. Turn it on only for a relay on your own network with a self-signed certificate. |

**Test connection** connects, logs in and disconnects. It sends no email. Use **Send test email** to prove a real send.

An SMTP provider reports "Sent" and never reports delivery. SMTP has no webhook.

## Google Workspace and Gmail

1. Turn on 2-Step Verification for the Google account.
2. Create an app password for it in the Google account's security settings. It has 16 characters. Some work, school
   and Advanced Protection accounts can't create one.
3. In AccessBay, use host `smtp.gmail.com`, port 465 with **SSL/TLS** (or port 587 with **STARTTLS**), the full email
   address as the **Username**, and the app password as the **Password**.

Google limits sending to about 2,000 messages a day. Google revokes the app password when the account's password
changes. Then create a new one and enter it in AccessBay.

## Microsoft 365

1. Turn on Authenticated SMTP for the mailbox. In the Microsoft 365 admin center, go to Users > Active users, select
   the user, then Mail > Manage email apps, and tick **Authenticated SMTP**. It stays off when security defaults are
   on.
2. In AccessBay, use host `smtp.office365.com`, port 587 and **STARTTLS**, with the mailbox address and password.

Microsoft is retiring basic authentication for SMTP. Check Microsoft's notice below for the dates. If your tenant no
longer allows it, use an API provider such as [Twilio SendGrid](/guide/providers/sendgrid/) or
[Resend](/guide/providers/resend/) instead.

## Your host's mailbox (cPanel and similar)

1. In your host's control panel, create a mailbox, or use an existing one.
2. Find the mailbox's "mail client settings" page. It lists the outgoing server name, the port and the encryption.
3. Enter those values in AccessBay, with the full mailbox address as the **Username**.

Some hosts limit the number of emails a mailbox can send per hour. Ask your host.

## Common errors

- **The login fails.** Check the user name, the password and that the port matches the encryption.
- **The connection times out.** Your server may block outgoing SMTP ports. Ask your host, or use an API provider.
- **A certificate error.** The host name must match the server's certificate. Use the name the provider gives you.
- **The server refuses the From address.** Most servers only allow the mailbox's own address.

## Vendor documentation

- [Google: send email from an app](https://knowledge.workspace.google.com/admin/gmail/send-email-from-a-printer-scanner-or-app)
- [Google: app passwords](https://support.google.com/accounts/answer/185833)
- [Microsoft: authenticated client SMTP submission](https://learn.microsoft.com/en-us/exchange/clients-and-mobile-in-exchange-online/authenticated-client-smtp-submission)
- [Microsoft: deprecation of basic authentication](https://learn.microsoft.com/en-us/exchange/clients-and-mobile-in-exchange-online/deprecation-of-basic-authentication-exchange-online)
