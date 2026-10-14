---
title: "AccessBay documentation"
description: "Guides and reference for AccessBay, the self-hosted WordPress store for software licences and digital goods."
---
import Card from '../../components/starlight/Card.astro';
import { withBase } from '../../lib/links';

AccessBay is in development. These pages describe what works in the current pre-release and say plainly when
something is coming in a later release.

<div class="card-grid">
	<Card title="User guide" icon="book-open" cta="Start here" href={withBase('guide/introduction/')}>
		What AccessBay needs, how to install it, and how to keep it healthy.
	</Card>
	<Card title="Developers" icon="file-braces" cta="Open the guide" href={withBase('developers/rest-conventions/')}>
		Authentication, errors and rate limits, hooks, extension points and the SDK.
	</Card>
	<Card title="API reference" icon="server" cta="Browse the routes" href={withBase('api/')}>
		Every REST route, generated from the same registry the plugin uses.
	</Card>
</div>

## User guide

For store owners and the people who run their sites: what AccessBay needs, how to install it, and how to keep it
healthy.

- [Introduction](/guide/introduction/): what AccessBay is and what works today.
- [Requirements](/guide/requirements/): WordPress, PHP and database versions.
- [Installation](/guide/installation/): install, activate and remove the plugin.
- [The secret key](/guide/secret-key/): the one setting you must make before going live.
- [Health checks](/guide/health-checks/): how AccessBay watches itself, and how your monitor can watch it.
- [Email setup](/guide/email/): how AccessBay sends email today.

## Developers

For developers who integrate with AccessBay.

- [REST conventions](/developers/rest-conventions/): authentication, errors, rate limits and headers.
- [Hooks](/developers/hooks/), [extension points](/developers/extension-points/) and the [SDK](/developers/sdk/).

## API reference

Every REST route, generated from the same registry the plugin uses: start with the
[API overview](/api/).
