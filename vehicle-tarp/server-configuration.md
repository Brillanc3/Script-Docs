---
description: config/server.lua - access, vehicle table, automation
icon: server
---

# Server configuration

`config/server.lua` is loaded **only on the server**. Clients never receive it.

## General

| Option                           | Default                                   | Meaning                                                                                                         |
| -------------------------------- | ----------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| `ServerConfig.Framework`         | `'auto'`                                  | `'auto'`, `'qbx'`, `'qb'`, `'esx'` or `'none'`. `'auto'` checks `qbx_core`, then `qb-core`, then `es_extended`. |
| `ServerConfig.MaxDistance`       | `6.0`                                     | Metres. Server-side maximum distance to cover / uncover (network tolerance included).                           |
| `ServerConfig.MaxSpeed`          | `0.5`                                     | m/s. Above this, a vehicle counts as moving and cannot be covered.                                              |
| `ServerConfig.RateLimits.action` | `{ capacity = 2, refillPerSecond = 0.5 }` | Anti-spam of the cover / uncover requests, per player.                                                          |
| `ServerConfig.DebugCommand`      | `'tarp_debug'`                            | Name of the diagnostics command (ace `command.<name>`).                                                         |

## Access

| Option                             | Default  | Meaning                                                                                                                |
| ---------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------- |
| `ServerConfig.Access`              | function | Decides who sees, uncovers and covers. **Must be written by you.** See [Access](access-model.md).                      |
| `ServerConfig.CoverRequiresAccess` | `true`   | Covering also needs `Access`. Setting `false` lets anyone make someone else's vehicle disappear.                       |
| `ServerConfig.AccessRecheck`       | `true`   | Every 2 s, tarps already shown are checked again: a lost key hides them. `false` if your scripts call `RefreshAccess`. |
| `ServerConfig.UncoverLockMinutes`  | `15`     | A tarp placed by a player cannot be uncovered by players before this delay. `0` = no delay. Staff are never delayed.   |

## Staff

```lua
ServerConfig.Staff = {
    ace = 'vehicle_tarp.admin',
    commands = { view = 'tarp_view', uncover = 'tarp_uncover', cover = 'tarp_cover' },
}
```

See [Commands](commands.md).

## Vehicle table

The framework vehicle table is the source of truth: a vehicle that is not in it cannot be covered, and customs are read back from it on uncover.

```lua
ServerConfig.VehicleTable = {
    preset = 'auto', -- 'auto' | 'qbx' | 'qb' | 'esx' | 'custom'
    custom = {
        table = 'player_vehicles',
        idColumn = 'id',          -- unique row id (the plate on ESX)
        plateColumn = 'plate',
        propsColumn = 'mods',
        ownerColumn = 'citizenid',
        format = 'ox',            -- 'ox' | 'qb' | 'esx': props format
        engineColumn = 'engine',  -- nil if missing
        bodyColumn = 'body',      -- nil if missing
    },
}
```

| Preset | Table             | Id      | Owner       | Props                  |
| ------ | ----------------- | ------- | ----------- | ---------------------- |
| `qbx`  | `player_vehicles` | `id`    | `citizenid` | `mods` (ox format)     |
| `qb`   | `player_vehicles` | `id`    | `citizenid` | `mods` (qb format)     |
| `esx`  | `owned_vehicles`  | `plate` | `owner`     | `vehicle` (esx format) |

Use `preset = 'custom'` for any other framework or table. Table and column names must be plain identifiers (letters, digits, `_`).

## Automation

| Option                             | Default | Meaning                                                                     |
| ---------------------------------- | ------- | --------------------------------------------------------------------------- |
| `AutoRecover.enabled`              | `true`  | Re-covers tracked vehicles left unused.                                     |
| `AutoRecover.idleMinutes`          | `30`    | Idle time before the re-cover.                                              |
| `AutoRecover.checkIntervalMs`      | `30000` | How often tracked vehicles are checked.                                     |
| `AutoRecover.playerRadius`         | `15.0`  | Never re-covers while a player is closer than this.                         |
| `AutoRecover.moveThreshold`        | `2.0`   | Metres moved between two checks = vehicle in use.                           |
| `ServerConfig.CoverOnShutdown`     | `true`  | On a txAdmin restart / stop, every registered vehicle still out is covered. |
| `ServerConfig.Persistence.ttlDays` | `30`    | A tarp not uncovered for this many days is deleted on start. `0` = never.   |

Details in [Storage and lifecycle](storage-and-lifecycle.md).

## Hooks

Three optional functions let another script create or delete the vehicle. Leave them as they are for a standalone setup.

| Hook                                                    | Standalone value                                          | Called                                                                                    |
| ------------------------------------------------------- | --------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| `ServerConfig.CreateVehicle(data)`                      | `nil` = native `CreateVehicleServerSetter`                | On uncover. Must return the server entity, or `nil`.                                      |
| `ServerConfig.DeleteVehicle(vehicle, record)`           | `nil` = `DeleteEntity`                                    | On cover, after the record is saved. The entity is deleted afterwards if it still exists. |
| `ServerConfig.OnVehicleRecreated(vehicle, row, source)` | sets the `vehicleid` state bag on QBox, nothing elsewhere | After a successful uncover. `source` is `nil` from the `Uncover` export.                  |

`data` contains `id`, `plate`, `model`, `vehicleType`, `coords` (vector4), `bucket`, `props`, `owner`, `source`. It runs in a thread: `Wait` is allowed. After `CreateVehicle`, the resource sets the routing bucket, applies the props and checks the plate - the vehicle is deleted if the plate does not match.

Example with a vehicle script: [mVehicle](examples/mvehicle.md).
