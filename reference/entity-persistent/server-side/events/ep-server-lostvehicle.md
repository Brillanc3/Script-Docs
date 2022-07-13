---
description: >-
  You can see this event in server/noescrowed.lua for fully personalisation. Is
  called for lost an vehicle in database.
---

# EP:Server:LostVehicle



```lua
#Server
TriggerEvent('EP:Server:LostVehicle', registery)

#Client
TriggerServerEvent('EP:Server:LostVehicle', registery)
```

### How work ?

{% hint style="warning" %}
It's an exemple !!!
{% endhint %}

```lua
local EP = exports['Entity-Persistent']:GetCore()
local vehicle = GetVehiclePedIsIn(GetPlayerPed(), true)

#client-side

EP.Functions.TriggerCallback('GetVehicleRegisteryKey', function(registeryKey)
    TriggerServerEvent('EP:Server:LostVehicle', registeryKey)
end, NetworkGetNetworkIdFromEntity(vehicle))

#server-side
-- For server side, prefer to use
local netId = NetworkGetNetworkIdFromEntity(vehicle)
local registery = EP.Functions.GetVehicleRegisteryKey(netId)
TriggerEvent('EP:Server:LostVehicle', registery)
```
