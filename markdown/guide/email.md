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

You can add as many providers as you like. Each has its own page with the steps to get its key and the values to enter:

- [Twilio SendGrid](/guide/providers/sendgrid/)
- [Mailgun](/guide/providers/mailgun/)
- [Postmark](/guide/providers/postmark/)
- [Resend](/guide/providers/resend/)
- [Brevo](/guide/providers/brevo/)
- [SMTP2GO](/guide/providers/smtp2go/)
- [ZeptoMail](/guide/providers/zeptomail/)
- [SMTP](/guide/providers/smtp/), including Google Workspace, Microsoft 365 and your host's mail server
- [WordPress (wp_mail)](/guide/providers/wp-mail/)

[Email providers](/guide/providers/overview/) helps you choose one.

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
secret in AccessBay as described on the provider's page. AccessBay refuses a webhook that fails the check.

`wp_mail`, SMTP and ZeptoMail have no webhook.

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
