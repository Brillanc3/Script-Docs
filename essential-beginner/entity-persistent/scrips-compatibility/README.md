# Scrips Compatibility

## qb-vehiclekeys

For persiste vehicle now use this Trigger variable

```lua
TriggerEvent('EP:Persist', vehicle, plate, {
    citizenIdKeys = QBCore.Functions.GetPlayerData().citizenid
})
```

After, for get all citizenId :

```lua
QBCore.Functions.CreateCallback('qb-vehiclekeys:server:GetVehicleKeys', function(source, cb)
    local citizenid = QBCore.Functions.GetPlayer(source).PlayerData.citizenid
    local keysList = {}
    for plate, citizenids in pairs (VehicleList) do
        if citizenids[citizenid] then
            keysList[plate] = true
        end
    end

    local VehiclesData = exports['Entity-Persistent']:GetData()
    for k, v in pairs(VehiclesData) do
        if v.citizenId == citizenid then
            keysList[v.plate] = true
        end
    end

    cb(keysList)
end)
```

