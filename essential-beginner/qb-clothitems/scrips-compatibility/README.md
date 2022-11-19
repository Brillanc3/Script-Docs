# Scrips Compatibility

## qs-inventory

Edit `function RemoveItem(source, item, amount, slot)`

```lua
function RemoveItem(source, item, amount, slot)
        local Player = QBCore.Functions.GetPlayer(source)
    
        if not Player then return false end
    
        amount = tonumber(amount) or 1
        slot = tonumber(slot)
    
        if slot then
            if Player.PlayerData.items[slot].amount > amount then
                Player.PlayerData.items[slot].amount = Player.PlayerData.items[slot].amount - amount
                Player.Functions.SetPlayerData("items", Player.PlayerData.items)
    
                if not Player.Offline then
                    TriggerEvent('qb-log:server:CreateLog', 'playerinventory', 'RemoveItem', 'red', '**' .. GetPlayerName(source) .. ' (citizenid: ' .. Player.PlayerData.citizenid .. ' | id: ' .. source .. ')** lost item: [slot:' .. slot .. '], itemname: ' .. Player.PlayerData.items[slot].name .. ', removed amount: ' .. amount .. ', new total amount: ' .. Player.PlayerData.items[slot].amount)
                end
    
                return true
            elseif Player.PlayerData.items[slot].amount == amount then
                if Player.PlayerData.items[slot].type:lower() == "cloth" then
                    TriggerClientEvent('qb-clothitems:Client:DropCloth', source, Player.PlayerData.items[slot].info)
                end

                Player.PlayerData.items[slot] = nil
                Player.Functions.SetPlayerData("items", Player.PlayerData.items)
    
                if Player.Offline then return true end
    
                TriggerEvent('qb-log:server:CreateLog', 'playerinventory', 'RemoveItem', 'red', '**' .. GetPlayerName(source) .. ' (citizenid: ' .. Player.PlayerData.citizenid .. ' | id: ' .. source .. ')** lost item: [slot:' .. slot .. '], itemname: ' .. item .. ', removed amount: ' .. amount .. ', item removed')
    
                return true
            end
        else
            local slots = GetSlotsByItem(Player.PlayerData.items, item)
            local amountToRemove = amount
    
            if not slots then return false end
    
            for _, _slot in pairs(slots) do
                if Player.PlayerData.items[_slot].amount > amountToRemove then
                    Player.PlayerData.items[_slot].amount = Player.PlayerData.items[_slot].amount - amountToRemove
                    Player.Functions.SetPlayerData("items", Player.PlayerData.items)
    
                    if not Player.Offline then
                        TriggerEvent('qb-log:server:CreateLog', 'playerinventory', 'RemoveItem', 'red', '**' .. GetPlayerName(source) .. ' (citizenid: ' .. Player.PlayerData.citizenid .. ' | id: ' .. source .. ')** lost item: [slot:' .. _slot .. '], itemname: ' .. Player.PlayerData.items[_slot].name .. ', removed amount: ' .. amount .. ', new total amount: ' .. Player.PlayerData.items[_slot].amount)
                    end
    
                    return true
                elseif Player.PlayerData.items[_slot].amount == amountToRemove then
                    if Player.PlayerData.items[_slot].type:lower() == "cloth" then
                        TriggerClientEvent('qb-clothitems:Client:DropCloth', source, Player.PlayerData.items[slot].info)
                    end
                    Player.PlayerData.items[_slot] = nil
                    Player.Functions.SetPlayerData("items", Player.PlayerData.items)
    
                    if Player.Offline then return true end
    
                    TriggerEvent('qb-log:server:CreateLog', 'playerinventory', 'RemoveItem', 'red', '**' .. GetPlayerName(source) .. ' (citizenid: ' .. Player.PlayerData.citizenid .. ' | id: ' .. source .. ')** lost item: [slot:' .. _slot .. '], itemname: ' .. item .. ', removed amount: ' .. amount .. ', item removed')
    
                    return true
                end
            end
        end
        return false
    end
```
