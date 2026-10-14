---
title: "REST conventions"
description: "The rules every AccessBay REST route follows, including authentication, errors, retries, rate limits and headers."
---
## Namespace

Every route lives under `accessbay/v1`:

```text
https://example.com/wp-json/accessbay/v1/…
```

With plain permalinks, use `https://example.com/?rest_route=/accessbay/v1/…`. The [API reference](/api/) lists every
route with its parameters and answers, generated from the same route registry the plugin uses.

## Who may call a route

Each route is one of three kinds.

| Kind | Who | How it authenticates |
|---|---|---|
| Admin | A logged-in WordPress user with the route's capability | The login cookie plus a `wp_rest` nonce in the `X-WP-Nonce` header, on every method |
| Public | Anyone | Nothing; rate limited |
| API | Machines, such as your own servers | Scoped API keys: coming in a later release. No API route exists yet |

For admin routes:

- Without a nonce, WordPress treats the request as anonymous: `401 accessbay_unauthenticated`.
- A nonce in the `_wpnonce` query parameter doesn't count: `403 accessbay_invalid_nonce`.
- Application Passwords are refused: `403 accessbay_application_password_refused`.
- A user without the route's capability gets `403 accessbay_forbidden`.

## Errors

Errors use WordPress's shape:

```json
{
  "code": "accessbay_rate_limited",
  "message": "Too many requests. Wait and try again.",
  "data": { "status": 429 }
}
```

Match on `code`, never on `message`, which is translated. Codes are AccessBay's own (below) or WordPress core's,
such as `rest_invalid_param`, `rest_no_route` and `rest_cookie_invalid_nonce`. Parameters are validated before
authorisation, so an invalid parameter answers `400 rest_invalid_param` even to a caller who may not use the route.

| Code | Status | Meaning |
|---|---|---|
| `accessbay_credential_in_url` | 400 | A credential (`key`, `license_key`, `token`, `api_key`) was sent in the query string |
| `accessbay_envelope_refused` | 400 | `?_envelope=` is not supported |
| `accessbay_method_override_refused` | 400 | `?_method=` and `X-HTTP-Method-Override` are not supported |
| `accessbay_unauthenticated` | 401 | No logged-in user |
| `accessbay_api_key_invalid` | 401 | The API key is missing or not valid |
| `accessbay_application_password_refused` | 403 | Application Passwords can't call AccessBay routes |
| `accessbay_invalid_nonce` | 403 | The `X-WP-Nonce` header is missing or not valid |
| `accessbay_forbidden` | 403 | The user lacks the route's capability |
| `accessbay_method_not_allowed` | 405 | The path exists but not with this method; the `Allow` header lists the methods it takes |
| `accessbay_rate_limited` | 429 | Too many requests; wait for the seconds in `Retry-After` |
| `accessbay_internal_error` | 500 | An unexpected failure; it is logged on the site |
| `accessbay_temporarily_unavailable` | 503 | Something AccessBay depends on is unavailable; try again later |

## Retrying

`503 accessbay_temporarily_unavailable` always means "try again later". It never means a key, licence or request is
invalid, so a client must not act on it as if it did (for example, by deactivating a licence). AccessBay answers it
when a health check has switched a feature off, when the site secret is missing in live mode or can't be read, and when
the rate limiter can't count. When the answer has a `Retry-After` header, wait that many seconds before retrying;
otherwise back off.

## Rate limits

Public routes are limited per client address and across the whole site (the health ping, for example, takes 60
requests a minute per client and 6000 a minute in total). Over a limit, the answer is `429 accessbay_rate_limited`
with `Retry-After` in seconds.

Limits count per client address. Behind a reverse proxy or CDN, AccessBay reads forwarding headers only from proxies you
trust (the `trusted_proxies` and `cloudflare_proxies` settings). Until you set them, every visitor arriving through the
proxy shares the proxy's address.

## Methods

- A path that exists but doesn't take the method answers `405` with an `Allow` header.
- `HEAD` works wherever `GET` does.
- Batch requests (`/batch/v1`) are refused for every AccessBay route.

## Headers

Every answer, errors included, sends `Cache-Control: no-store, private` and `Vary: Cookie`, and no
`Access-Control-*` headers: no AccessBay route is meant to be called from a browser on another site. JSONP is off.

## Filtering list routes

The admin screens that list things (products, orders, licences, activations, the email log and the site mail log)
read their rows from list routes. These routes take a `POST` with a JSON body, like every admin route (see "Who may
call a route" above). All body fields are optional.

| Route | Filters that take several values | Other body fields |
|---|---|---|
| `/accessbay/v1/products/list` | `status`: `draft`, `active`, `archived` | `before_id`, `limit` |
| `/accessbay/v1/orders/search` | `source`: `woocommerce`, `native`, `manual`, `api`; `payment_status`: `pending`, `paid`, `partially_refunded`, `refunded`, `charged_back`, `cancelled`; `fulfilment_status`: `unfulfilled`, `partially_fulfilled`, `fulfilled`, `partially_revoked`, `revoked`, `not_required` | `q` (up to 191 characters), `page`, `limit` |
| `/accessbay/v1/licenses/search` | `status`: `unstarted`, `active`, `expired`, `void`, `suspended`, `revoked`; `mode`: `test`, `live`; `key_origin`: `generated`, `imported` | `product_id`, `customer_id`, `license_key`, `before_id`, `limit` |
| `/accessbay/v1/activations/search` | `state`: `live`, `deactivated` | `staging` (true or false), `page`, `limit` |
| `/accessbay/v1/admin/outbox/list` | `status`: `queued`, `held`, `accepted`, `suppressed`, `failed`, `cancelled`, `bounced`; `delivery`: `deferred`, `delivered`, `bounced`, `complained` | `provider_id`, `before_id`, `limit` |
| `/accessbay/v1/admin/mail/site-log` | `result`: `sent`, `failed` | `recipient` (up to 190 characters), `before_id`, `limit` |

`limit` is at most 100. The [API reference](/api/) has the full list of fields and answers for each route.

Each filter in the first column accepts one value, a comma-separated string or an array:

```json
{ "status": "active,draft", "limit": 25 }
```

```json
{ "status": ["active", "draft"], "limit": 25 }
```

Both bodies ask for products that are active or draft. The rules:

- Values inside one filter are alternatives (OR). A row matches when it has any of them.
- Different filters combine (AND). This body asks for orders that are paid or partly refunded, and also not yet
  fulfilled:

  ```json
  {
    "payment_status": ["paid", "partially_refunded"],
    "fulfilment_status": "unfulfilled"
  }
  ```

- A single value works as it always did. An empty string, an empty array or `null` means no filter.
- Spaces around a value are removed and a repeated value counts once.
- An unknown value answers `400 rest_invalid_param`, so a typo never turns into "no filter" silently.
- A list longer than the number of allowed values is refused.
