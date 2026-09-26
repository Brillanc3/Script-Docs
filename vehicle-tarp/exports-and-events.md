---
description: Connect a garage, a key script or an interaction script
icon: code
---

# Exports and events

## Server exports

| Export                    | Returns            | Use                                                                                             |
| ------------------------- | ------------------ | ----------------------------------------------------------------------------------------------- |
| `IsTarped(id)`            | `boolean`          | Is this vehicle table id covered?                                                               |
| `GetTarp(id)`             | table or `nil`     | Copy of the tarp record.                                                                        |
| `Cover(vehicle, license)` | `boolean`          | Covers a vehicle entity. No access check, no delay. **Call from a thread.**                     |
| `Uncover(id)`             | `netId` or `nil`   | Recreates the vehicle. No access check, no delay. **Call from a thread.**                       |
| `ClearTarp(id)`           | `boolean`          | Deletes the record without recreating the vehicle.                                              |
| `Track(netId, playerId)`  | `boolean`          | Adds a vehicle out to the tracking list (auto re-cover, staff panel).                           |
| `Untrack(filter)`         | `boolean`, `count` | Removes matching vehicles from the tracking list. See [Untrack](exports-and-events.md#untrack). |
| `RefreshAccess(playerId)` | `boolean`          | Checks this player's access now.                                                                |

`id` is always the **vehicle table id**: `player_vehicles.id` on QBox / QBCore, the plate on ESX.

## Untrack

`Untrack` removes vehicles out from the tracking list: they are no longer re-covered automatically and leave the staff panel. It never deletes the entity and never touches tarps.

```lua
exports.vehicle_tarp:Untrack({ netId = netId })          -- one vehicle, by entity
exports.vehicle_tarp:Untrack({ plate = plate })          -- one vehicle, by plate
exports.vehicle_tarp:Untrack({ owner = license })        -- every vehicle of a player (wipe, ban)
exports.vehicle_tarp:Untrack({ owner = license, plate = plate })
```

| Field   | Matches                                                                                                |
| ------- | ------------------------------------------------------------------------------------------------------ |
| `netId` | The vehicle entity.                                                                                    |
| `plate` | The plate - padding and case do not matter.                                                            |
| `owner` | The license (`license:...`) of the player passed to `Track`, or of the player who covered the vehicle. |

Fields are combined: every field you give must match. It returns `true` if at least one vehicle was untracked, then the number of vehicles:

```lua
local untracked, count = exports.vehicle_tarp:Untrack({ owner = license })
```

{% hint style="warning" %}
`Untrack` **raises an error** on an empty filter, a non-table, or an unknown field - even next to a valid one. `Untrack({ plate = p, ownerId = o })` fails instead of untracking on the plate alone.
{% endhint %}

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
-- entity already deleted? use the plate:
exports.vehicle_tarp:Untrack({ plate = plate })
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
