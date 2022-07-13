# EP:Client:setProperties



```lua
--[[ Set some properties. Exemple : ]]
local props = QBCore.Functions.GetVehicleProperties(vehicle)
local netId = NetworkGetNetworkIdFromEntity(vehicle)
TriggerEvent('EP:Client:setProperties', netId, props)
```
