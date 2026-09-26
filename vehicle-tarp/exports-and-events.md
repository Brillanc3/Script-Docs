---
description: Connect a garage, a key script or an interaction script
icon: code
---

# Exports and events

## Server exports

| Export                       | Returns          | Use                                                                         |
| ---------------------------- | ---------------- | --------------------------------------------------------------------------- |
| `IsTarped(id)`               | `boolean`        | Is this vehicle table id covered?                                           |
| `GetTarp(id)`                | table or `nil`   | Copy of the tarp record.                                                    |
| `Cover(vehicle, license)`    | `boolean`        | Covers a vehicle entity. No access check, no delay. **Call from a thread.** |
| `Uncover(id)`                | `netId` or `nil` | Recreates the vehicle. No access check, no delay. **Call from a thread.**   |
| `ClearTarp(id)`              | `boolean`        | Deletes the record without recreating the vehicle.                          |
| `Track(netId, playerId)`     | `boolean`        | Adds a vehicle out to the tracking list (auto re-cover, staff panel).       |
| `Untrack({ netId = netId })` | `boolean`        | Removes it from the tracking list.                                          |
| `RefreshAccess(playerId)`    | `boolean`        | Checks this player's access now.                                            |

`id` is always the **vehicle table id**: `player_vehicles.id` on QBox / QBCore, the plate on ESX.

## Server events

```lua
AddEventHandler('vehicle_tarp:covered', function(id, plate)
    -- the vehicle was deleted, the tarp is in place
end)

AddEventHandler('vehicle_tarp:uncovered', function(id, plate, netId)
    -- the vehicle was recreated
end)
```

## Client exports and events

| Name                          | Type   | Use                                                                                |
| ----------------------------- | ------ | ---------------------------------------------------------------------------------- |
| `GetNearestTarp()`            | export | `{ id, plate, distance }` of the nearest tarp within `InteractDistance`, or `nil`. |
| `IsTarpNearby(id)`            | export | Is this tarp known by the client (in range and visible)?                           |
| `vehicle_tarp:client:uncover` | event  | `TriggerEvent('vehicle_tarp:client:uncover', id)` - same as pressing `E`.          |
| `vehicle_tarp:client:stats`   | event  | `TriggerEvent('vehicle_tarp:client:stats', id)` - toggles the stats card.          |

With your own interaction script, set `Config.Prompt = false`. Example: [ox\_target](examples/ox-target.md).

## Connecting a garage

{% stepper %}
{% step %}
### When the garage spawns a vehicle

```lua
exports.vehicle_tarp:Track(NetworkGetNetworkIdFromEntity(vehicle), playerId)
```

The vehicle is re-covered after inactivity and appears in the staff panel. The tracking list is in memory: after a reboot, only `Track` adds a vehicle back.
{% endstep %}

{% step %}
### When the garage stores a vehicle

```lua
exports.vehicle_tarp:Untrack({ netId = NetworkGetNetworkIdFromEntity(vehicle) })
```
{% endstep %}

{% step %}
### Stop a covered vehicle from leaving the garage

The resource does not block garages. Check before spawning:

```lua
if exports.vehicle_tarp:IsTarped(vehicleId) then
    return -- or: notify "this vehicle is under a tarp"
end
```
{% endstep %}
{% endstepper %}
