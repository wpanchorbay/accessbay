---
title: "Roadmap"
description: "Where AccessBay stands today and where it is heading, from a WooCommerce licensing layer to a complete store for software and digital goods."
---
import RoadmapStrip from '../../components/RoadmapStrip.astro';
import RoadmapItem from '../../components/RoadmapItem.astro';
import Card from '../../components/starlight/Card.astro';
import { EXTERNAL_LINKS } from '../../lib/links';

AccessBay runs on WooCommerce today and grows into a full store for software and digital goods. Everything without a Pro tag is free, and the free plugin works fully on its own. Next, Then and Later show the order we plan to build in, without dates.

<RoadmapStrip />

## Sell

<ul class="rm-list not-content">
	<RoadmapItem name="WooCommerce" status="shipped">Paid orders create licences, and refunds and disputes reverse them.</RoadmapItem>
	<RoadmapItem name="Standalone shop" status="then">Checkout, refunds, invoices and tax rates, with or without WooCommerce.</RoadmapItem>
	<RoadmapItem name="Coupons" status="then">Discount codes built into the checkout.</RoadmapItem>
	<RoadmapItem name="Offers" status="later" pro>Buy X get Y, tiered discounts, add-ons and follow-up deals.</RoadmapItem>
</ul>

## Deliver

<ul class="rm-list not-content">
	<RoadmapItem name="Licensing" status="shipped">Keys, activations and seats for every sale.</RoadmapItem>
	<RoadmapItem name="Updates" status="shipped">One-click updates in wp-admin, through download links that expire.</RoadmapItem>
	<RoadmapItem name="Customer portal" status="shipped">Buyers see their licences, sites and downloads without asking you.</RoadmapItem>
	<RoadmapItem name="Email delivery" status="shipped">Licence emails still arrive if one mail provider fails.</RoadmapItem>
	<RoadmapItem name="SDK" status="beta">Licensing and updates inside your own plugin or theme.</RoadmapItem>
	<RoadmapItem name="Digital goods" status="next">Protected files with download limits, and refunds that stop access.</RoadmapItem>
</ul>

## Renew

<ul class="rm-list not-content">
	<RoadmapItem name="Renewals" status="next">Expiry reminders and a renewal link that extends the same licence.</RoadmapItem>
	<RoadmapItem name="Subscriptions" status="then" pro>Recurring billing with retries, upgrades and seat changes.</RoadmapItem>
</ul>

## Grow

<ul class="rm-list not-content">
	<RoadmapItem name="Automated campaigns" status="later" pro>Email sequences that run on their own, including abandoned carts.</RoadmapItem>
	<RoadmapItem name="Affiliates" status="later" pro>Referral links, commissions on renewals and payouts.</RoadmapItem>
	<RoadmapItem name="Resellers and agencies" status="later" pro>Sell licence pools to resellers and agencies, who issue licences to their own clients.</RoadmapItem>
</ul>

## Run

<ul class="rm-list not-content">
	<RoadmapItem name="Admin" status="shipped">Products, orders, licences and health in one place, with search and saved views.</RoadmapItem>
	<RoadmapItem name="REST API and WP-CLI" status="shipped">Connect your own tools with keys limited to what they need.</RoadmapItem>
	<RoadmapItem name="Setup wizard" status="next">A guided first run that gets a new store ready to sell.</RoadmapItem>
	<RoadmapItem name="EU VAT and invoices" status="later" pro>VAT tools, PDF invoices and credit notes.</RoadmapItem>
	<RoadmapItem name="Webhooks and reports" status="later" pro>Outbound webhooks, recurring revenue and cohort reports.</RoadmapItem>
	<RoadmapItem name="White-label" status="later" pro>Your branding in place of ours.</RoadmapItem>
</ul>

Also on our list: more payment gateways, connectors for other stores and licence transfer between customers. Plans change as we learn, and the [changelog](/changelog/) shows what has actually been released.

<div class="card-grid">
	<Card title="Missing something?" icon="message-square-plus" cta="Tell us" href={EXTERNAL_LINKS.requestFeature}>
		Request a feature or share your opinion. What you tell us helps decide what comes next.
	</Card>
</div>
