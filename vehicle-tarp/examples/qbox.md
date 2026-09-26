---
description: qbx_core, qbx_vehicles and a garage
icon: cube
---

# QBox

**What you get:** the vehicle owner covers and uncovers their vehicles; garages see the vehicle again after an uncover.

{% stepper %}
{% step %}
### Framework and vehicle table

Nothing to change. With `ServerConfig.Framework = 'auto'` and `VehicleTable.preset = 'auto'`, QBox is detected and `player_vehicles` is used.

Check in game: `tarp_debug whoami` must print `framework=qbx` and your `citizenid`.
{% endstep %}

{% step %}
### Access: the owner

```lua
ServerConfig.Access = function(source, tarp, action, cache)
    cache = cache or {}
    if cache.citizenid == nil then
        local player = exports.qbx_core:GetPlayer(source)
        cache.citizenid = player and player.PlayerData.citizenid or false
    end
    return cache.citizenid ~= false and cache.citizenid == tarp.vehicleOwner
end
```

Want lent keys too? Add the [ox\_inventory key item](ox-inventory.md) check in the same function.
{% endstep %}

{% step %}
### Garage link after uncover

Keep the default `OnVehicleRecreated`: it sets the `vehicleid` state bag, so `qbx_vehicles` and garages recognise the recreated vehicle.

```lua
ServerConfig.OnVehicleRecreated = function(vehicle, row, source)
    if TarpBridge.Framework() ~= 'qbx' then return end
    Entity(vehicle).state:set('vehicleid', tonumber(row.id) or row.id, false)
end
```
{% endstep %}

{% step %}
### Garage: track, untrack, block

In your garage script, server side:

```lua
-- vehicle taken out
exports.vehicle_tarp:Track(NetworkGetNetworkIdFromEntity(vehicle), source)

-- vehicle stored
exports.vehicle_tarp:Untrack({ netId = NetworkGetNetworkIdFromEntity(vehicle) })

-- before taking a vehicle out (vehicleId = player_vehicles.id)
if exports.vehicle_tarp:IsTarped(vehicleId) then return end
```
{% endstep %}
{% endstepper %}

{% hint style="warning" %}
**`qbx_vehiclekeys` cannot give access to a tarp.** Its keys are bound to the entity, which does not exist while the vehicle is covered. For lent keys, use a key item bound to the plate.
{% endhint %}
