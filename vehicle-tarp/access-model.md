---
description: Who sees a tarp, who uncovers it, who covers
icon: key
---

# Access

One function decides everything: `ServerConfig.Access`, in `config/server.lua`. The resource reads no inventory and no key script by itself - **you** plug your rule in here.

```lua
ServerConfig.Access = function(source, tarp, action, cache)
    return false -- true = allowed
end
```

{% hint style="danger" %}
Return exactly `true` to allow. Anything else, **including an error** in the function, is a refusal.
{% endhint %}

## Arguments

| Argument | Meaning                                                                                                                                                                   |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `source` | Server id of the player.                                                                                                                                                  |
| `tarp`   | The vehicle: see the table below.                                                                                                                                         |
| `action` | `'view'` (see the tarp), `'uncover'` or `'cover'`.                                                                                                                        |
| `cache`  | Table shared by all `view` checks of one player during one sync. Store what you read there (inventory, identifier) to read it only once. `nil` for `uncover` and `cover`. |

| `tarp` field   | Meaning                                                                                                     |
| -------------- | ----------------------------------------------------------------------------------------------------------- |
| `id`           | Row id in the vehicle table (`player_vehicles.id`, or the plate on ESX). Shown in "Uncover vehicle ID #12". |
| `plate`        | Plate, **trimmed and upper case**. Compare with `TarpUtil.NormalizePlate(yourPlate)`.                       |
| `model`        | Model hash.                                                                                                 |
| `owner`        | License of the player who covered. `nil` on `cover`.                                                        |
| `vehicleOwner` | Owner column of the vehicle table (`citizenid` on QBox / QBCore, `identifier` on ESX).                      |

## Standalone rule: the owner

Works on QBox, QBCore and ESX, without any other script. `TarpBridge.GetIdentifier(source)` returns the value stored in the owner column for this player.

```lua
ServerConfig.Access = function(source, tarp, action, cache)
    cache = cache or {}
    if cache.identifier == nil then
        cache.identifier = TarpBridge.GetIdentifier(source) or false
    end
    return cache.identifier ~= false and cache.identifier == tarp.vehicleOwner
end
```

Want lent keys, key items or jobs? Put that check in the same function. Ready-made versions: [ox\_inventory key item](examples/ox-inventory.md), [mVehicle](examples/mvehicle.md).

{% hint style="warning" %}
**A covered vehicle has no entity.** It was deleted - that is the point of the tarp. Anything bound to the entity (session keys such as `qbx_vehiclekeys`, state bags, lockpick state) cannot answer here. Use data that exists without the vehicle: the owner, or an item bound to the plate.
{% endhint %}

## When is access checked?

| Moment                                | Action    | Notes                                                                          |
| ------------------------------------- | --------- | ------------------------------------------------------------------------------ |
| Every 2 s, for tarps near each player | `view`    | Players without access receive nothing. Checks are spread over several frames. |
| Player presses `E` / `/untarp`        | `uncover` | Re-checked on the server.                                                      |
| Player runs `/tarp`                   | `cover`   | Only if `ServerConfig.CoverRequiresAccess = true`.                             |

### Refreshing visibility

* `ServerConfig.AccessRecheck = true` (default): tarps already shown are checked again every 2 s. A lost key hides them within 2 s. Nothing else to do.
* `ServerConfig.AccessRecheck = false`: tarps already shown stay until you call `exports.vehicle_tarp:RefreshAccess(playerId)`. Cheaper on big servers - call it from your key or inventory script when access changes. New tarps in range are always checked.

## Uncover delay

`ServerConfig.UncoverLockMinutes = 15`: a tarp placed by a player cannot be uncovered by a player for 15 minutes (message "Recent tarp: can be uncovered in X min"). This stops a thief from covering a vehicle to steal it right after.

* Tarps placed by staff, by the auto re-cover or by the `Cover` export have no delay.
* Staff can always uncover.
* `0` disables the delay.

## Staff

Staff (ace `vehicle_tarp.admin`) are **not** added to `Access` automatically. They use their own tools: `tarp_view` to see every tarp, `tarp_uncover` / `tarp_cover`, and the staff panel. See [Commands](commands.md).

## Testing a rule

```
tarp_debug access <playerId> <id>
```

Prints the `view` result of `Access` for this player and this tarp.
