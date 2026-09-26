---
description: Dependencies, start order, database and admin permissions
icon: download
---

# Installation

{% stepper %}
{% step %}
### Check the dependencies

**`oxmysql` is required.** It is declared in the manifest and the resource will not start without it.

**`ox_lib` is recommended but not required.** With it, a tarped vehicle keeps its colours, extras, doors, windows and tyres, and the `[E]` prompt uses the ox\_lib help text. Without it the resource still runs, on a reduced snapshot and the native prompt.

A framework (`qbx_core` or `qb-core`) and `ox_inventory` are optional too. Detection is automatic at resource start - there is no config flag to pick a mode.
{% endstep %}

{% step %}
### Start the resource

Drop the `vehicle_tarp` folder in your resources and start it **after** `oxmysql` and your framework:

```
ensure oxmysql
ensure ox_lib
ensure qbx_core
ensure vehicle_tarp
```

If your server uses bracketed group folders, putting it in `[standalone]` is enough - the existing `ensure [standalone]` line already covers it, as long as `[ox]` and `qbx_core` start before it.
{% endstep %}

{% step %}
### Database

Nothing to import. The tables are created automatically on first start through `oxmysql`, and there is no `.sql` file shipped with the resource.
{% endstep %}

{% step %}
### Admin permissions

**On `qbx_core`** there is nothing to do. `Config.adminGroups` (`{ 'admin', 'god' }` by default) is checked through `exports.qbx_core:HasPermission`.

**On standalone or plain `qb-core`**, the admin commands fall back to the ACE principal `command.vtarp`. Add this line to `permissions.cfg`, or wherever your ACE grants live:

```
add_ace group.admin command.vtarp allow
```
{% endstep %}

{% step %}
### Wire up the two things that are yours

The resource does not spawn vehicles and ships no access rule you can use as-is:

1. Call `exports.vehicle_tarp:Track` from your garage or spawn code - see [Tracking a vehicle](tracking-a-vehicle.md).
2. Write `ServerConfig.canAccess` in `config/server.lua` - see [Access model](access-model.md).

{% hint style="warning" %}
Until `canAccess` returns `true` for someone, **no tarp is visible or uncoverable by anyone**. That is the expected state of a fresh install, not a bug.
{% endhint %}
{% endstep %}
{% endstepper %}

## When to restart what

| Changed                                     | Needed                 |
| ------------------------------------------- | ---------------------- |
| Anything in `client/`, `server/`, `shared/` | `restart vehicle_tarp` |
| `config/shared.lua` or `config/server.lua`  | `restart vehicle_tarp` |
| `fxmanifest.lua`, or the map tiles          | Full server restart    |
