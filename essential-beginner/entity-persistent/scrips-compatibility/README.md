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
local EP = exports['Entity-Persistenqt']:GetData()
for k, v in pairs(EP) do
    if v.citizenIdKeys == "citizenId or license" then
        -- Action
    end
end
```

