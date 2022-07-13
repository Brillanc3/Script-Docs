# EP.Functions.CreateVehicle

{% hint style="info" %}
You can remove

QBCore.Functions.SpawnVehicle(model, function(veh) end, location, true)

by our function for exemple
{% endhint %}

```lua
EP.Functions.CreateVehicle(model --[[string or integer]], function(vehicle)
-- Client function. You can add properties here whith any framworks

TriggerServerEvent('EP:Server:SaveToDb', NetworkGetNetworkIdFromEntity(vehicle), model, coords, props or {}, registeryKey)
end, coords --[[ vector4 ]], registeryKey --[[ UniqueKey, default plate ]])
```

{% hint style="info" %}
For lost un vehicle after spawn, call this TriggerEvent :&#x20;

TriggerServerEvent('EP:Server:LostVehicle', registery)
{% endhint %}

### Exemples with ESX or QBus



```lua
-- Do not use SpawnVehicle then remove or hide with " -- "
-- QBCore.Functions.SpawnVehicle('adder', function(veh)
--    SetVehicleNumberPlateText(veh, 'TEST')
--    SetEntityHeading(veh, coords.w)
--    exports['LegacyFuel']:SetFuel(veh, 100.0)
--    TaskWarpPedIntoVehicle(PlayerPedId(), veh, -1)
--    TriggerEvent("vehiclekeys:client:SetOwner", GetVehicleNumberPlateText(veh))
--    SetVehicleEngineOn(veh, true, true)
--end, coords, true)

EP.Functions.CreateVehicle('adder', function(veh)
    SetVehicleNumberPlateText(veh, 'TEST')
    SetEntityHeading(veh, coords.w)
    exports['LegacyFuel']:SetFuel(veh, 100.0)
    TaskWarpPedIntoVehicle(PlayerPedId(), veh, -1)
    TriggerEvent("vehiclekeys:client:SetOwner", GetVehicleNumberPlateText(veh))
    SetVehicleEngineOn(veh, true, true)
    local netid = NetworkGetNetworkIdFromEntity(veh)
    TriggerServerEvent('EP:Server:SaveToDb', netid, GetEntityModel(veh), coords, {}, GetVehicleNumberPlateText(veh))
end, coords, 'TEST')
```
