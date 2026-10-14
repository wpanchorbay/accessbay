---
title: "Orders"
description: "The Orders screen, what each column and status means, and how to filter orders."
---
Go to AccessBay > Orders to see the orders AccessBay knows about. The list is read-only and shows the newest order
first. Orders from WooCommerce and from checkout appear here as they are placed. Search, filters, saved views and
shareable links are described in [Search and filters](/guide/finding-things/).

## Columns

| Column | What it shows |
|---|---|
| Order | The order number. Select it to open the order. |
| Customer | The buyer's email address. |
| Source | Where the order came from: WooCommerce, AccessBay checkout, Manual or API. |
| Payment | Where the money stands. A "Disputed" tag appears next to it when the buyer has opened a dispute. |
| Fulfilment | Whether the buyer has received what they bought, such as a licence. |
| Total | The order total in its currency. |
| Placed | The day the order was placed. |

You can hide any column with the columns button. This list has no sort by column.

## Payment

- **Pending:** the payment has not come in yet.
- **Paid:** the payment came in.
- **Partly refunded** and **Refunded:** part of the money, or all of it, went back to the buyer.
- **Charged back:** the buyer's bank took the money back.
- **Cancelled:** the order was cancelled.

## Fulfilment

- **Not fulfilled:** nothing has been delivered yet.
- **Partly fulfilled** and **Fulfilled:** some or all of the order has been delivered.
- **Partly revoked** and **Revoked:** some or all of what was delivered has been taken back.
- **Nothing to fulfil:** the order has nothing to deliver.

## Filters and search

- **Source:** WooCommerce, AccessBay checkout, Manual, API.
- **Payment:** Pending, Paid, Partly refunded, Refunded, Charged back, Cancelled.
- **Fulfilment:** Not fulfilled, Partly fulfilled, Fulfilled, Partly revoked, Revoked, Nothing to fulfil.

Each filter takes several values, and different filters combine. For example, "Payment: Paid, Partly refunded" together
with "Fulfilment: Not fulfilled" lists paid orders that haven't been delivered.

The search box finds an order by its number or by the buyer's email address.
