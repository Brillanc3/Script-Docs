---
description: The one export you need to call
---

# Tracking a vehicle

`vehicle_tarp` never spawns vehicles itself. Your garage, dealership or spawn code hands it a vehicle that already exists and is networked, and the resource takes over persistence and tarping from there.

## The export

```lua
-- server-side, once the vehicle entity exists and is networked
exports.vehicle_tarp:Track(netId, ownerSource)
```

| Argument      | Type     | Meaning                                                             |
| ------------- | -------- | ------------------------------------------------------------------- |
| `netId`       | `number` | The vehicle's network id - `NetworkGetNetworkIdFromEntity(vehicle)` |
| `ownerSource` | `number` | The player server id to record as the owner of the row              |

Returns the row id of the tracked vehicle, or `nil` if `netId` did not resolve to an entity.

This is the **only** entry point. There is no client-side export and no event to trigger instead.

## Example

```lua
-- your garage resource, server-side
RegisterNetEvent('myGarage:server:takeOut', function(vehicleId)
    local src = source
    local netId = spawnVehicleForPlayer(src, vehicleId) -- your own spawn code

    exports.vehicle_tarp:Track(netId, src)
end)
```

From that moment the vehicle is saved server-side - position, heading, health, fuel, mods, colours, extras, doors, windows and tyres - and starts its idle countdown.

## What `ownerSource` does, and what it does not

`ownerSource` is resolved through the framework bridge and stored as the row's `owner`: the `citizenid` on `qbx_core` / `qb-core`, the player's `license:` identifier standalone.

It grants **no key and no item**. Whether that player - or anyone else - can later see and uncover the vehicle is decided entirely by `ServerConfig.canAccess`, which receives `owner` as one of its arguments. Recording an owner and granting access are two separate decisions, and it is up to your resolver to connect them if you want them connected - see [Access model](access-model.md).

## Tracking a vehicle by hand

`/vtarp_track` tracks the vehicle the calling admin is currently sitting in. It exists so a vehicle can be injected into the system without a garage integration - useful while testing, not a replacement for the export.

## Undoing it

`exports.vehicle_tarp:Untrack` is the counterpart of `Track`: it drops the row without touching the entity, so your garage can store the vehicle back and take it out of the system in the same breath - see [Untracking a vehicle](untracking-a-vehicle.md).
