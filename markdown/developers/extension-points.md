---
title: "Extension points"
description: "How other plugins can extend AccessBay, today and later."
---
AccessBay has no public extension points for third-party plugins yet. The only plugin that extends it is AccessBay Pro,
which uses the surface listed [below](#accessbay-pro).

Internally, AccessBay is built from modules that add their own REST routes, settings, health checks and background
jobs through shared contracts. Those contracts are not a public API: they can change in any release, and a third-party
plugin that uses them may break on update.

Today, integrate through the [REST API](/developers/rest-conventions/), which is versioned (`accessbay/v1`) and
documented route by route in the [API reference](/api/). Public extension points, with the same care for
compatibility, are coming in a later release and will be listed here.

## AccessBay Pro

AccessBay Pro is the one plugin that reaches into AccessBay (Free) today, through the surface below. This section is
the contract between the two.

### Version handshake

- Pro declares the oldest Free it works with (`REQUIRES_FREE` in `accessbay-pro.php`) and compares it with the
  **public constant `AccessBay\PLUGIN_VERSION`**, which Free defines when its main file loads and keeps defined.
- When Free is missing or older than that, Pro stands down. It registers no hook, adds nothing to Free, shows an admin
  notice and never fatals. Pro registers all its hooks from one gate on `plugins_loaded` priority 0, so a hook added
  later inherits the stand-down.
- The check is one-directional. Pro checks Free; Free does not check Pro. Free keeps a public item for one minor
  version after deprecating it, which is what lets an older Pro keep working. The first time Free removes a public item
  that a released Pro uses, add a minimum-Pro check in Free or a maximum-Free check in Pro.
- The Free/Pro version matrix suite (`47-pro-matrix.sh`) simulates upgrading in either order with a copy of Pro that
  needs a newer Free. It does not perform a real plugin update.

### Free names Pro may use

Anything else in AccessBay is internal. Pro's tests fail on a `AccessBay\` name that is not listed here, so adding a
name is a reviewed change to this list.

- `AccessBay\PLUGIN_VERSION` — the version handshake above.
- `AccessBay\Platform\Application\Database\Connection` — the database connection a migration runs on.
- `AccessBay\Platform\Application\Migrations\Migration` — a numbered migration.
- `AccessBay\Platform\Application\Migrations\MigrationSet` — one plugin's migrations under its own slug.
- `AccessBay\Platform\Application\Migrations\Step` — one step of a migration.
- `AccessBay\Platform\Application\Migrations\DdlStep` — a step that runs table DDL.
- `AccessBay\Platform\Application\Migrations\CreateTable` — a table definition.
- `AccessBay\Platform\Application\Migrations\AlterTable` — a step that adds a column when it is missing.
- `AccessBay\Platform\Infrastructure\Migrations\InnoDbTables` — the shared check that a migration's tables exist and are
  InnoDB. It lives under `Infrastructure` for now; it moves to `Platform\Application\Migrations` in a later release.
- `AccessBay\Platform\Presentation\Admin\PluginActionLinks` — the "Manage" link on a plugin's row.
- `AccessBay\Plugin` — only the scaffold marker constant; removed with Pro's placeholder class.
- `AccessBay\Licensing\Application\Activations` — the activation counts per plugin version, for Pro's versions report.
- `AccessBay\Platform\Presentation\Rest\AddOnRoutes` — the add-on route manifest an add-on returns from `accessbay/rest/add_ons`.
- `AccessBay\Platform\Presentation\Rest\AddOnRoute` — one route in that manifest.
- `AccessBay\Platform\Presentation\Rest\HttpMethod` — the method of an add-on route.

### Free hook Pro uses

- `accessbay/platform/migration_sets` (filter) — Pro appends its `MigrationSet`. Free reads the filter in its
  `plugins_loaded` priority 1 schema upgrade, so a plugin must register its callback by priority 0.
- `accessbay/delivery/release_channels` (filter) — Pro adds the `beta` channel (`latest-beta.json`).
- `accessbay/rest/add_ons` (filter) — Pro adds its routes' manifest (`/pro/...`, operation ids `pro*`).
