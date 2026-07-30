---
description: config/shared.lua
---

# Configuration

Everything below lives in `config/shared.lua` and is read once at resource start, so any change needs a `restart vehicle_tarp`.

The access rules live in `config/server.lua` instead.

## Tarping

| Key                      | Default              | Meaning                                                                                                                                                                                                  |
| ------------------------ | -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `enableTarp`             | `true`               | Master switch. `false` disables tarping entirely: vehicles stay visible forever, and any previously tarped vehicle is respawned on boot.                                                                 |
| `tarpIdleMinutes`        | `30`                 | Minutes a vehicle may sit unoccupied before it is tarped automatically.                                                                                                                                  |
| `tarpAlpha`              | `128`                | Opacity of the tarp prop, `0`-`255`. `128` is roughly 50%.                                                                                                                                               |
| `untarpDistance`         | `3.0`                | Metres a player must be within for the `[E]` prompt to appear.                                                                                                                                           |
| `renderReconcileSeconds` | `5`                  | How often a client re-checks which tarps it should be seeing. Self-heals a render missed right after a restart, and hides a tarp once it is out of range or no longer accessible. `0` disables the poll. |
| `accessRefreshItems`     | `{ car_key = true }` | Inventory items whose count change refreshes tarp visibility immediately, instead of waiting for the poll. Requires `ox_inventory`.                                                                      |

{% hint style="warning" %}
`enableTarp = false` is not free at startup: every tracked vehicle in the database is respawned when the resource starts, staggered 50 ms apart. On a large fleet the last vehicle appears `50 ms x rows` after boot. That is the price of the mode, not a fault.
{% endhint %}

{% hint style="info" %}
A vehicle that keeps **moving** is never tarped - the idle counter resets on movement, not only on someone sitting in it. This is what keeps a vehicle on a trailer from being tarped mid-drive.
{% endhint %}

## Saving

None of these change what players see. They only govern how much movement a hard crash can cost you.

| Key                   | Default | Meaning                                                                                                  |
| --------------------- | ------- | -------------------------------------------------------------------------------------------------------- |
| `snapshotSeconds`     | `5`     | How often position, heading, healths, fuel and dirt level are re-read for active vehicles.               |
| `moveThreshold`       | `1.0`   | Metres a vehicle must move before its position counts as having changed.                                 |
| `propsRefreshMinutes` | `5`     | How long a full pass over the expensive properties takes - mods, colours, extras, doors, windows, tyres. |
| `kvpFlushSeconds`     | `30`    | Flush cadence of the local buffer. This is exactly the crash-loss window.                                |
| `dbFlushSeconds`      | `900`   | Cadence of the batched write to MySQL.                                                                   |

## Admin and diagnostics

| Key                   | Default              | Meaning                                                                                                                                        |
| --------------------- | -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| `adminGroups`         | `{ 'admin', 'god' }` | Framework permission groups allowed to use the admin commands on `qbx_core`. Standalone servers use the ACE principal `command.vtarp` instead. |
| `debugRefreshSeconds` | `30`                 | How often the staff map and the debug view are refreshed. An admin action also forces an immediate push.                                       |
| `debugDrawDistance`   | `150.0`              | Metres beyond which the debug view stops drawing boxes and labels. Blips are unaffected and cover the whole map.                               |
| `map`                 | measured             | World-to-map projection used by `/vtarp_ui`. Setting `originX`, `originY` and `scale` back to `nil` reopens the map's calibration mode.        |

## Convars

Set these in `server.cfg`.

| Convar                       | Effect                                                                                                                                                                                                     |
| ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `set vehicle_tarp-locale fr` | Loads `locales/fr.json`, falling back to `locales/en.json` for any missing key. Default is `en`.                                                                                                           |
| `set vehicle_tarp-debug 1`   | Registers `/vtarp_selftest`, which runs the resource's self-tests and prints the result to the server console. Leave it off in production - the command is not registered at all when the convar is unset. |
