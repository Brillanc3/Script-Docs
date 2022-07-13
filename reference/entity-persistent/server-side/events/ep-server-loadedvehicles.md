# EP:Server:LoadedVehicles

Is call with EventHandler 'onResourceStart' of FiveM for respawn all vehicles

```lua
TriggerEvent('EP:Server:LoadedVehicles', vehicles)
```

```lua
-- Exemple :
local vehicle = EP.Functions.GetVehicles()
TriggerEvent('EP:Server:LoadedVehicles', vehicles)
```
