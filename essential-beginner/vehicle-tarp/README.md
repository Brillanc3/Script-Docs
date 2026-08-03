---
description: Vehicle persistence and idle tarping, with a server-side access resolver
---

# Vehicle Tarp

`vehicle_tarp` persists vehicles server-side and hides idle ones under a translucent tarp that only the players you allow can see and uncover.

Once a vehicle is handed to the resource, it is tracked in one of two states:

| State    | What it means                                                                                                                                                                                                                    |
| -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ACTIVE` | The vehicle entity exists in the world and drives normally.                                                                                                                                                                      |
| `TARPED` | After `tarpIdleMinutes` with nobody in it, the real entity is deleted server-side and replaced client-side by a translucent prop, drawn **only** for the players your access resolver allows. Everyone else sees nothing at all. |

An allowed player standing within `untarpDistance` of a tarp presses `[E]` to uncover it. The vehicle respawns with its saved state re-applied: position, heading, health, fuel, mods, colours, extras, doors, windows and tyres.

## What you have to wire up

Two things, and only two:

1. [**Tracking**](tracking-a-vehicle.md) - call `exports.vehicle_tarp:Track` from your garage, dealership or spawn code once the vehicle exists.
2. [**Access**](access-model.md) - write `ServerConfig.canAccess` to decide who sees and uncovers a tarp.

Everything else works out of the box. When your garage stores a vehicle back, or a player is wiped, call [`Untrack`](untracking-a-vehicle.md) to drop it from the system.

## Where to go next

| Page                                            | What it covers                                                |
| ----------------------------------------------- | ------------------------------------------------------------- |
| [Installation](installation.md)                 | Dependencies, start order, database, admin permissions        |
| [Tracking a vehicle](tracking-a-vehicle.md)     | The `Track` export, and what `ownerSource` does               |
| [Untracking a vehicle](untracking-a-vehicle.md) | The `Untrack` export, its filter, and what it leaves behind   |
| [Access model](access-model.md)                 | `canAccess`, render distance, key-item refresh, admin logging |
| [Configuration](configuration.md)               | Every key in `config/shared.lua`, plus the convars            |
| [Commands](commands.md)                         | Admin commands, the staff map, the diagnostic overlay         |

## Dependencies

{% hint style="info" %}
**`oxmysql` is the only hard requirement** - without it the resource will not start. **`ox_lib` is recommended but not required**: it is what lets a tarped vehicle keep its colours, extras, doors, windows and tyres. Everything else is optional.
{% endhint %}

| Dependency             | Required?    | With it                                                                                                                       | Without it                                                                                                                                                              |
| ---------------------- | ------------ | ----------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `oxmysql`              | **Required** | -                                                                                                                             | The resource will not start                                                                                                                                             |
| `ox_lib`               | Recommended  | Full vehicle snapshot: colours, extras, doors, windows, tyres, and the ox\_lib `[E]` help text                                | Reduced snapshot, and the help text falls back to the native prompt                                                                                                     |
| `qbx_core` / `qb-core` | Optional     | Owner identity is the `citizenid`, notifications go through the framework, admin commands use the framework permission groups | Standalone mode: owner identity is the player's `license:` identifier, notifications go through `chat:addMessage`, admin commands use the ACE principal `command.vtarp` |
| `ox_inventory`         | Optional     | `accessRefreshItems` refreshes tarp visibility the instant a key item changes hands                                           | Visibility refreshes on the reconcile poll only                                                                                                                         |

Detection is automatic at resource start. There is no config flag to pick a mode.
