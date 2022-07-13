---
description: Registery is unique key for no dupe in data base. Default is vehicle plate.
---

# Registery

### How get registery

```lua
-- Client-Side | Server-Side
EP.Functions.TriggerCallback('GetVehicleRegisteryKey', function(registeryKey)
                
end, NetworkGetNetworkIdFromEntity(vehicle))

-- For server side, prefer to use
local registery = EP.Functions.GetVehicleRegisteryKey(netId)
```
