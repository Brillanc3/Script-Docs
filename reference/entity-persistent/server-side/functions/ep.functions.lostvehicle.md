---
description: Callback is called before deleting in database
---

# EP.Functions.LostVehicle

```etlua
EP.Functions.LostVehicle(registeryKey, cb)
-- CB have all data of registery vehicle :
-- @coords    - vector4
-- @netId     - integer
-- @props     - table (need json.decode(@props))
-- @registery - unique key
```

```lua
-- Exemple (logs is already implement)
RegisterNetEvent('MyPersonnalLost', function(registeryKey, reason)
    EP.Functions.LostVehicle(registeryKey, function(data)
        EP.Logs("Lost " .. data.netId .. " netId vehicle with reason : " .. tostring(reason))
    end)
end)

TriggerServerEvent('MyPersonnalLost', registeryKey, "come out garage")
```
