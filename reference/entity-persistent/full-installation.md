---
description: Everything you need to know to make your vehicle persist!
---

# Full Installation

## Make a vehicle persistent

{% hint style="info" %}
**Reminder:** RegisterKey is the unique key that makes it possible to make your vehicle unique in the database and avoid duplications.
{% endhint %}

### Method 1 - With function

```lua
EP.Functions.CreateVehicle(
    model --[[hash key or name]],
    cb, --[[ Client Callback, return cretated vehicle]]
    coords --[[vector 4]],
    registerKey --[[ your unique string ]]
)
```

{% hint style="danger" %}
For use this function, you need to remove actually framworks SpawnVehicle method.

Exemple :&#x20;

ESX.Game.SpawnVehicle(modelOrHash, coords, heading, cb)\
QBCore.Functions.SpawnVehicle(model, cb, coords, isnetworked)
{% endhint %}

#### Exemple to use

```lua
--[[ For setup vector4 write : ]]
local coords = vector3(x, y, z)
local heading = coords.heading
local newCoords = vector4(coords.x, coords.y, coords.z, heading)


EP.Functions.CreateVehicle('jester2', function(vehicle)
    SetVehicleNumberPlateText(vehicle, "MyPlate")
    exports['LegacyFuel']:SetFuel(veh, 100.0)
    TaskWarpPedIntoVehicle(PlayerPedId(), veh, -1)
end, newCoords , "MyPlate")
```

### Method 2 - With TriggerEvent

```lua
TriggerServerEvent('EP:Server:SaveToDb',
    netId, --[[ network integer ]]
    modelHash, --[[ model integer of vehicle]]
    location, --[[ vector4 ]]
    props, --[[ properties table  ]]
    registeryKey --[[ your unique string ]]
)
```

{% hint style="info" %}
It is generally used in the callback of framworks functions except in exceptional cases.
{% endhint %}

#### Exemple to use

```lua
local coords = QBCore.Functions.GetCoords(PlayerPedId())

QBCore.Functions.SpawnVehicle('adder', function(veh)
    SetVehicleNumberPlateText(veh, 'TEST')
    SetEntityHeading(veh, coords.w)
    exports['LegacyFuel']:SetFuel(veh, 100.0)
    TaskWarpPedIntoVehicle(PlayerPedId(), veh, -1)
    TriggerEvent("vehiclekeys:client:SetOwner", GetVehicleNumberPlateText(veh))
    SetVehicleEngineOn(veh, true, true)
    local netId = NetworkGetNetworkIdFromEntity(veh)
    TriggerServerEvent('EP:Server:SaveToDb', netId, GetEntityModel('adder'), coords, {}, 'TEST')
end, coords, true)
```

## Remove a vehicle from persistence

### Method 1 - With function

{% hint style="info" %}
This function automatically removes the vehicle from the database and removes the vehicle.\
**It is replaced when you see :**\
<mark style="color:green;">ESX.Game.DeleteVehicle(vehicle)</mark> or <mark style="color:green;">QBCore.Functions.DeleteVehicle(vehicle)</mark>
{% endhint %}

```lua
EP.Functions.DeleteVehicle(
    vehicle --[[ Entity ]]
)
```
