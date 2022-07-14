---
description: >-
  You can see this event in server/noescrowed.lua for fully personalisation. Is
  called for lost an vehicle in database.
---

# EP:Server:LostVehicle



```lua
#Server
TriggerEvent('EP:Server:LostVehicle', NetworkGetNetworkIdFromEntity(vehicle))

#Client
TriggerServerEvent('EP:Server:LostVehicle', NetworkGetNetworkIdFromEntity(vehicle))
```
