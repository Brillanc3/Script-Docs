# Scrips Issues

## [#qb-vehiclekeys](scrips-issues.md#qb-vehiclekeys "mention")

```sql
-- Import this inside persistent vehicles table
ALTER TABLE `persistent_vehicles`
    ADD `citizenIdKeys` VARCHAR(11) NULL AFTER `plate`;
```

Edit fxmanifest.lua

```lua
-- server_script 'server/main.lua'
server_scripts {
    '@oxmysql/lib/MySQL.lua',
    'server/main.lua'
}
```

Edit file : /server/main.lua

```lua
-- Add :
RegisterNetEvent('qb-vehiclekeys:server:GiveVehicleKeysWithPersistent', function(playerId)
    local Player = QBCore.Functions.GetPlayer(playerId)
    local result = MySQL.query('SELECT * FROM persistent_vehicles WHERE citizenIdKeys = ?', {Player.PlayerData.citizenid})
    if result then
        for k, v in pairs(result) do
            GiveKeys(Player.PlayerData.source, v.plate)
        end
    end
end)
```

Edit file : /client/main.lua

```lua
-- Edit 
RegisterNetEvent('QBCore:Client:OnPlayerLoaded', function()
    GetKeys()
    TriggerServerEvent('vehiclekeys:server:GiveVehicleKeysWithPersistent', QBCore.Functions.GetPlayerData().source)
end)
AddEventHandler('onResourceStart', function(resourceName)
    if resourceName == GetCurrentResourceName() and QBCore.Functions.GetPlayerData() ~= {} then
        GetKeys()
        TriggerServerEvent('qb-vehiclekeys:server:GiveVehicleKeysWithPersistent', QBCore.Functions.GetPlayerData().source )
    end
end)
```
