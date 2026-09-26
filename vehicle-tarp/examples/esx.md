---
description: es_extended and owned_vehicles
icon: e
---

# ESX

**What you get:** the vehicle owner covers and uncovers their vehicles from `owned_vehicles`.

{% stepper %}
{% step %}
### Framework and vehicle table

Nothing to change: `es_extended` is detected and the `esx` preset is used.

| Preset `esx` | Value                                  |
| ------------ | -------------------------------------- |
| Table        | `owned_vehicles`                       |
| Id           | `plate` - the tarp id **is the plate** |
| Owner        | `owner` (player identifier)            |
| Props        | `vehicle`, esx format                  |
{% endstep %}

{% step %}
### Access: the owner

```lua
ServerConfig.Access = function(source, tarp, action, cache)
    cache = cache or {}
    if cache.identifier == nil then
        local xPlayer = exports.es_extended:getSharedObject().GetPlayerFromId(source)
        cache.identifier = xPlayer and xPlayer.identifier or false
    end
    return cache.identifier ~= false and cache.identifier == tarp.vehicleOwner
end
```
{% endstep %}

{% step %}
### Check the damage keys

Damage is saved with the keys `tyreBurst` and `windowsBroken`. If your ESX version uses other keys, burst tyres and broken windows will not come back.
{% endstep %}
{% endstepper %}

{% hint style="info" %}
`OnVehicleRecreated` does nothing on ESX by default. Use it if your garage needs a state bag on the recreated vehicle.
{% endhint %}
