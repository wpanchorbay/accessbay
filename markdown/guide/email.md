---
title: "Email setup"
description: "How AccessBay sends email, how to add email providers, what happens when one fails, and what the email logs keep."
---
AccessBay sends its own emails, such as the licence key after a purchase, through a list of email providers. You
choose the providers and their order. The list starts with one provider, WordPress mail (`wp_mail`), so email works
before you set anything up.

## Start with wp_mail

`wp_mail()` is WordPress's own mail function. AccessBay uses it by default, and it works with any SMTP or
email-service plugin that routes `wp_mail()` through a mail service. If you already have such a plugin, you don't
need to change anything.

AccessBay never replaces `wp_mail()` for the rest of your site. Your other plugins send mail as they did before.

Many hosts deliver mail from PHP poorly or not at all. With `wp_mail` alone, AccessBay can only report that WordPress
accepted an email. It can't say whether the email arrived.

## Why add a second provider

If `wp_mail` is your only provider and it fails, nothing else takes over. A second provider lets AccessBay send the
email another way. The Email providers screen shows an "Add a second email provider" message while `wp_mail` is the
only one, and the "AccessBay email delivery" check in Site Health warns about the same thing.

A customer who buys a licence still sees the licence key and download links on screen after the purchase, so a
failing provider doesn't leave them stuck.

## Add a provider

1. Go to AccessBay > Settings > Email providers.
2. Select **Add provider**.
3. Pick the service you will send through, then select **Continue**.
4. Fill in the form. It has groups: **Sending identity** (**Name**, **From address**, **From name**; the last two are
   optional), **Connection**, **Credentials** and, for providers that report delivery, **Webhook**. **Back** returns
   to the list of services.
5. Select **Save and test**. AccessBay saves the provider and runs a connection test. If the test fails, the dialog
   stays open and shows the reason, so you can fix the fields and try again. **Save** stores the provider without a
   test.

The **Status**, **Last check** and **Last test** columns of the table show the results.

AccessBay stores keys, tokens and passwords encrypted and never shows them again. To change one, open the provider
with **Edit** and enter the new value.

Each provider also needs its sender or sending domain verified in the provider's own account. Without that, the
provider can refuse to send.

You can add as many providers as you like. They are the types below.

### SMTP

Use SMTP for any service or server that takes mail over SMTP, such as a host's mail server, Amazon SES or
ZeptoMail's SMTP service. SMTP2GO has its own provider type, below.

Enter the **SMTP host**, the **Port** (587 for STARTTLS, 465 for SSL), the **Encryption** (**STARTTLS** or
**SSL/TLS**), and the **Username** and **Password** if the server needs them. **Allow a local server** lets the host
be a loopback or private address with a self-signed certificate, such as a relay on your own network. It applies to
that provider only.

**Test connection** connects, logs in and disconnects. It sends no email. An SMTP provider reports "Sent" and never
reports delivery.

### Brevo

1. In Brevo, create an API key. Enter it as the **API key**.
2. Verify your sender or domain in Brevo.
3. For delivery events, add a webhook in Brevo with the URL from AccessBay (see "Webhooks"). Set its bearer
   authentication to a token you choose, and enter the same token as the **Webhook bearer token**.

**Test connection** checks the key against your Brevo account. It sends no email.

### Resend

1. In Resend, create an API key. Enter it as the **API key**.
2. Verify your sending domain in Resend.
3. For delivery events, add a webhook in Resend with the URL from AccessBay. Copy its secret (it starts with
   `whsec_`) into **Webhook signing secret**.

**Test connection** checks the key. A key that can only send is accepted. It sends no email.

### Postmark

1. In Postmark, copy the server's API token. Enter it as the **Server API token**.
2. Leave **Message stream** empty to use the default transactional stream, or enter the stream's name.
3. Verify your sender or domain in Postmark.
4. For delivery events, add a webhook in Postmark. Postmark checks webhooks with basic authentication, so put a user
   name and password in the URL from AccessBay, and enter the same pair as **Webhook username** and **Webhook
   password**.

**Test connection** checks the token. It sends no email.

### SMTP2GO

1. In SMTP2GO, go to Sending > API Keys and create an API key with the **Emails** send permission. Enter it as the
   **API key**.
2. Leave **Region** on **Automatic** unless your SMTP2GO account is bound to a data region. The other choices are
   United States, Europe and Australia.
3. Verify your sender or domain in SMTP2GO. The **From address** must be a verified sender.
4. For delivery events, add a webhook in SMTP2GO. SMTP2GO doesn't sign webhooks, so put a user name and password in
   the URL from AccessBay (`https://user:password@host/...`), and enter the same pair as **Webhook username** and
   **Webhook password**. Also tick the custom headers in the webhook settings, so events carry the message ID.

AccessBay reports these events: delivered, hard bounce, soft bounce, spam, unsubscribe and reject.

SMTP2GO has no way to check a key without sending. **Test connection** sends a test email to your own admin address.
It proves the key, the region and the verified sender.

### ZeptoMail (Zoho CPaaS)

1. In ZeptoMail, create a mail agent and copy its Send Mail token. Enter it as the **Send Mail token**. The
   "Zoho-enczapikey" prefix is optional.
2. Leave **API host** empty to use `api.zeptomail.com`. If your account is in another region, enter that host, for
   example `api.zeptomail.eu`.
3. Verify your sending domain in ZeptoMail.
4. For delivery events, add a webhook in ZeptoMail with the URL from AccessBay. Copy its secret key into **Webhook
   signing key**.

ZeptoMail has no way to check a token without sending. **Test connection** sends a test email to your own admin
address. ZeptoMail doesn't report that an email was delivered.

### Mailgun (API)

1. In Mailgun, create an API key. Enter it as the **API key**.
2. Enter the **Sending domain** you verified in Mailgun, for example `mg.example.com`.
3. Choose the **Region** your account is in: US (`api.mailgun.net`) or EU (`api.eu.mailgun.net`).
4. For delivery events, add an HTTP webhook in Mailgun with the URL from AccessBay. Copy the HTTP webhook signing
   key (Mailgun > Sending > Webhooks) into **Webhook signing key**.

**Test connection** checks the key and the sending domain. It sends no email.

### SendGrid (API)

1. In SendGrid, create an API key with the Mail Send permission. Enter it as the **API key**.
2. Choose the **Region** your account is in: Global (`api.sendgrid.com`) or EU (`api.eu.sendgrid.com`).
3. Verify your sender or domain in SendGrid.
4. For delivery events, turn on the Event Webhook in SendGrid with the URL from AccessBay. Turn on signature
   verification and copy its public key into **Webhook verification key**.

**Test connection** checks that the key can send mail. It sends no email.

## Test a provider

There are two tests. Both are buttons in the **Actions** column of the Email providers table.

- **Test connection** checks the provider's key or login. For most types it sends no email. ZeptoMail and SMTP2GO
  can't check without sending, so for them it sends a test email to your own admin address.
- **Send test email** (the send button in the row) opens a dialog titled "Send a test email via" the provider's name.
  The **Send to** field starts with your own address, and you can use any address. It sends one short test email
  through that provider only. If it fails,
  no other provider takes over. AccessBay refuses an address on the Suppressions list. Each admin can send 10 an
  hour, and the whole site has a cap per hour as well. The email shows in the Email log as a test.

If a test fails because of the address, the rate limit or a refused message, the provider's health stays as it was.

## Webhooks

A webhook lets a provider tell AccessBay what happened to an email after it accepted it: delivered, bounced or
complained about. Without it, the email stays at "Sent".

For every provider that supports webhooks, the Email providers screen has a **Webhooks** card. It shows a **Webhook
URL** for that provider with a copy button. Paste it into the provider's webhook settings, then enter the matching
secret in AccessBay as described for each type above. AccessBay refuses a webhook that fails the check.

`wp_mail` and SMTP have no webhook.

## How failover, retries and alerts work

**Order.** AccessBay tries the providers in the order of the table. Use the **Move up** and **Move down** buttons to
change it. The first provider that accepts the email wins.

**Failover.** If a provider fails, AccessBay tries the next one straight away. After three failures in ten minutes,
AccessBay pauses that provider for five minutes. The pause doubles each time up to an hour, and the **Status** column
shows "Paused until" the time. A provider is also paused at once if its key is refused or its account is suspended. It is
tried again after you change its settings or **Test connection** passes. If its credits or quota run out, it is
paused until the provider resets them or for an hour, and AccessBay records an alert at once. Each time a
provider goes down or recovers, AccessBay records one alert.

**Retries.** If every provider fails, AccessBay tries the whole list again after 1 minute, 5 minutes, 15 minutes, 1
hour, 3 hours, 6 hours, 12 hours and then daily. It stops after the **Email retry period** in Settings > Email, which
is 3 days by default and can be 1 to 5. The first time every provider fails, AccessBay records an alert, once per outage. When the retry
period is spent, the email is marked failed and an alert is recorded.

**A bad address.** If a provider says the address itself can't receive mail, AccessBay doesn't try other providers.
It marks the email failed and adds the address to the Suppressions list.

**When the outcome is unknown.** If a connection drops after the email was sent, AccessBay can't tell whether the
provider took it. Brevo and Resend accept a repeat of the same email safely, so AccessBay asks the same provider
again. For the other providers, AccessBay marks the email "Possible duplicate" and carries on with the next round,
because a duplicate email is better than a lost licence email.

**Site Health.** The "AccessBay email delivery" check warns when `wp_mail` is your only provider, a provider is
paused or failed its last test, a provider's credits ran out, a provider with a webhook has reported nothing for 7
days although it accepted email, an email failed every retry, or the primary provider or the site's own mail failed
in the last 24 hours.

## Monitoring and alerts

**What is checked.** Once an hour AccessBay checks every enabled provider. If a provider can check its credentials
without sending, AccessBay does that. `wp_mail` and the providers that can't (ZeptoMail and SMTP2GO) are judged from
their real sends. The **Status** and **Last check** columns of the Email providers table show the result. The
**Monitoring and alerts** card shows when the last check ran and when the next one runs.

**Daily test send.** It is off by default, because it sends real email. To turn it on, switch on **Send a daily test
email** in Settings > Email. AccessBay then sends a short test email through each provider once a day. It goes to
**Daily test email recipient**, or to the site administrator if that is empty. The Site Health check warns if a
provider refuses it.

**Who is told.** When a provider goes from working to failing, AccessBay shows a notice on its own screens and emails
the addresses in **Mail problem alerts** in Settings > Email. Enter up to ten, separated by commas or new lines. If
it is empty, the email goes to the site administrator. It is sent through a provider that works, never the failing
one, or through plain `wp_mail()` if none does. While the problem lasts, AccessBay sends one reminder every 24 hours
at most. When the provider works again, it sends one more email. If every enabled provider fails, that counts as its
own problem, and it closes when one provider works again.

**The notice.** A red box at the top of the Email providers screen says "Email delivery has a problem" (or "Email
delivery has problems" when there are several) and lists each affected provider.

Addresses in error text, alerts and alert emails are masked, for example `j***@example.com`. A staging copy holds
alerts under its send guard instead of emailing them.

## The email log

Go to AccessBay > Email log. It has three tabs.

- **AccessBay email** lists every email AccessBay sends, with its status. Open one to see its **Attempts** (each
  provider tried, the result and how long it took) and its **Provider events** (delivered, bounced and so on). Use
  **Retry** to put a failed or held email back in the queue, **Cancel email** to stop one that is waiting, and
  **Resend** to send a new copy. A resend is built again from current data, and the original stays in the log.
- **All site email** lists every email your site sends with `wp_mail`, from any plugin, with its recipient, subject,
  time and result.
- **Suppressions** lists addresses AccessBay won't send to: those that bounced or complained, and those added by
  hand. **Lift suppression** removes one.

The logs keep recipients, subjects, times and results. They don't keep the body of any email.

### Retention

AccessBay keeps all of it by default. In Settings > Email, **Email record retention** is 0, which means keep
everything. Enter a number of days to delete older AccessBay emails, their attempts, provider events and site-mail
log entries. An email still waiting to be sent is never deleted.

### Privacy export and erase

AccessBay adds a person's emails to the WordPress personal data exporter and eraser under Tools. The export covers
the mail log entries, attempts, provider events, site-mail log entries and suppressions for the person's address.
The eraser deletes emails that were never sent and replaces the address in the rest with a placeholder. It keeps
ids, status and timing. A suppressed address stays suppressed.
