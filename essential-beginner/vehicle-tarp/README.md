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

Everything else works out of the box.

## Where to go next

| Page                                        | What it covers                                                |
| ------------------------------------------- | ------------------------------------------------------------- |
| [Installation](installation.md)             | Start order, database, admin permissions                      |
| [Tracking a vehicle](tracking-a-vehicle.md) | The `Track` export, and what `ownerSource` does               |
| [Access model](access-model.md)             | `canAccess`, render distance, key-item refresh, admin logging |
| [Configuration](configuration.md)           | Every key in `config/shared.lua`, plus the convars            |
| [Commands](commands.md)                     | Admin commands, the staff map, the diagnostic overlay         |

## Dependencies

| Dependency             | Required? | With it                                                                                                                       | Without it                                                                                                                                                              |
| ---------------------- | --------- | ----------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `oxmysql`              | **Yes**   | -                                                                                                                             | The resource will not start                                                                                                                                             |
| `qbx_core` / `qb-core` | Optional  | Owner identity is the `citizenid`, notifications go through the framework, admin commands use the framework permission groups | Standalone mode: owner identity is the player's `license:` identifier, notifications go through `chat:addMessage`, admin commands use the ACE principal `command.vtarp` |
| `ox_lib`               | Optional  | Full vehicle snapshot: colours, extras, doors, windows, tyres                                                                 | Reduced snapshot, and the `[E]` help text falls back to the native prompt                                                                                               |
| `ox_inventory`         | Optional  | `accessRefreshItems` refreshes tarp visibility the instant a key item changes hands                                           | Visibility refreshes on the reconcile poll only                                                                                                                         |

Detection is automatic at resource start. There is no config flag to pick a mode.
