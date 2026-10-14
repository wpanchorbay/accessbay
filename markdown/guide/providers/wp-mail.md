---
title: "WordPress (wp_mail)"
description: "How the WordPress mail provider works in AccessBay, and how to use it with WP Mail SMTP, FluentSMTP or a similar plugin."
---
**WordPress mail (wp_mail)** hands the email to WordPress's own mail function, `wp_mail()`. It is on the list of
providers from the start, so email works before you set anything up.

## When to choose it

Choose it when a mail plugin, such as WP Mail SMTP, FluentSMTP or Post SMTP, should send AccessBay's emails. Any plugin
that routes `wp_mail()` carries them. AccessBay never replaces `wp_mail()` for the rest of your site.

If no such plugin is installed, WordPress sends with PHP's mail function. Many hosts deliver that poorly or not at all.

## Before you start

Set up the mail plugin first, with the service or SMTP login you want. Follow that plugin's own guide, and send a test
from the plugin. The plugin's service needs a verified sender or domain, like any provider in this guide.

## Fill in AccessBay

There is nothing to fill in. The provider has no key, host or password. It uses the From address of the email, which
the plugin may override.

**Test connection** sends one test email to your own admin address. Use **Send test email** to send to another
address.

## Delivery events (webhook)

Not available. AccessBay can only report that WordPress accepted the email. It can't say whether it arrived. For
delivery events, add an API provider ([Twilio SendGrid](/guide/providers/sendgrid/), [Mailgun](/guide/providers/mailgun/),
[Postmark](/guide/providers/postmark/), [Resend](/guide/providers/resend/), [Brevo](/guide/providers/brevo/) or
[SMTP2GO](/guide/providers/smtp2go/)).

## Common errors

- **AccessBay says "Sent" but the email never arrives.** The plugin can report success while its service rejects the
  message. Check the plugin's own email log, and AccessBay's Email log.
- **Emails go to spam.** The sender or domain isn't authenticated at the plugin's service.
- **"Add a second email provider" notice.** With only `wp_mail`, nothing takes over if it fails. Add another provider.

## Vendor documentation

Your mail plugin's own documentation. WordPress's reference for the function is [wp_mail()](https://developer.wordpress.org/reference/functions/wp_mail/).
