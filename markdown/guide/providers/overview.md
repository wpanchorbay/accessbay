---
title: "Email providers"
description: "Which email provider to choose for AccessBay, where the webhook URL is shown, how to test, and a setup page for each provider."
---
AccessBay can send through several email providers at once. This page helps you pick one. Each provider has its own
page with the steps to get its key, the values to enter in AccessBay, and what to do when something fails. For the
rest of the email system (failover, retries, alerts, the email log), see [Email setup](/guide/email/).

## Which provider to choose

| You want to | Choose |
|---|---|
| Keep using a mail plugin such as WP Mail SMTP, FluentSMTP or Post SMTP | [WordPress (wp_mail)](/guide/providers/wp-mail/) |
| Send through a mailbox you already have (your host, Google Workspace, Microsoft 365) | [SMTP](/guide/providers/smtp/) |
| Send through a transactional email service and see delivered and bounced emails in AccessBay | [Twilio SendGrid](/guide/providers/sendgrid/), [Mailgun](/guide/providers/mailgun/), [Postmark](/guide/providers/postmark/), [Resend](/guide/providers/resend/), [Brevo](/guide/providers/brevo/) or [SMTP2GO](/guide/providers/smtp2go/) |
| Use Zoho's service | [ZeptoMail](/guide/providers/zeptomail/) (no delivery events in this version) |

If you have no preference, pick a service from the third row. Those providers can tell AccessBay when an email was
delivered, bounced or marked as spam. `wp_mail`, SMTP and ZeptoMail can only report that the email was accepted.

Choose **WordPress (wp_mail)** when a plugin such as WP Mail SMTP or FluentSMTP should do the sending. AccessBay hands
the email to WordPress, and the plugin sends it with the settings you gave the plugin. You don't enter any key in
AccessBay for this.

It is a good idea to add at least two providers. If one fails, AccessBay tries the next.

## Steps that every provider needs

1. Create an account with the provider.
2. Verify the sender address or the sending domain in the provider's account. Most providers refuse an email from an
   address they have not verified. Each provider page says how.
3. Create a key (or token, or password) for sending.
4. In AccessBay, go to Settings > Email providers, select **Add provider**, pick the provider and fill in the form.
   The provider page lists each field.
5. Select **Save and test**.
6. Optional: set up the webhook so the provider reports delivery. See below.

## Where the webhook URL is shown

A webhook lets the provider tell AccessBay that an email was delivered, bounced or reported as spam.

Each provider that supports webhooks has its own URL. In AccessBay, go to Settings > Email providers and find the
**Webhooks** card. It lists a **Webhook URL** for the provider, with a copy button. Paste that URL into the provider's
webhook settings. Then enter the secret the provider gives you (or the user name and password you chose) in the
provider's form in AccessBay.

The URL belongs to one provider. If you add two providers of the same type, each has its own URL.

AccessBay refuses a webhook call that fails the check. If the provider's dashboard shows an HTTP 401 answer from
your site, the secret in AccessBay does not match the one in the provider.

`wp_mail`, SMTP and ZeptoMail have no webhook.

## Test a provider

Two buttons in the **Actions** column of the Email providers table help you check a setup:

- **Test connection** checks the key or login. For most providers it sends no email. SMTP2GO and ZeptoMail can't
  check a key without sending, so for them it sends one test email to your own admin address.
- **Send test email** sends one short email to an address you type, through that provider only. If it fails, no other
  provider takes over.

If **Save and test** fails, the dialog stays open and shows the reason. Fix the field and try again. Keys, tokens and
passwords are stored encrypted and are never shown again. To change one, select **Edit** and enter the new value.

## The provider pages

- [Twilio SendGrid](/guide/providers/sendgrid/)
- [Mailgun](/guide/providers/mailgun/)
- [Postmark](/guide/providers/postmark/)
- [Resend](/guide/providers/resend/)
- [Brevo](/guide/providers/brevo/)
- [SMTP2GO](/guide/providers/smtp2go/)
- [ZeptoMail](/guide/providers/zeptomail/)
- [SMTP](/guide/providers/smtp/) (Google Workspace and Gmail, Microsoft 365, your host)
- [WordPress (wp_mail)](/guide/providers/wp-mail/)

Vendors change their dashboards. If a menu name here does not match what you see, use the vendor's documentation
linked on each page.
