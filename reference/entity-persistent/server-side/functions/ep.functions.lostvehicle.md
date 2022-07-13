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
EP.Functions.LostVehicle(registeryKey, function(data)
    local props = json.decode(data.props)
    EP.Logs("vehicle " .. data.netId .. " have " .. props.plate .. " plate.")
end)
```
