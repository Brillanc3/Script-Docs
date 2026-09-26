---
description: Access with a key item bound to the plate
icon: key
---

# ox\_inventory key item

**What you get:** anyone holding the key item of the vehicle sees its tarp and can uncover it - a lent key works. Losing the key hides the tarp.

This assumes a key item (here `carkey`) whose **metadata contains the plate**: `metadata.plate`.

{% stepper %}
{% step %}
### Access: the key item

```lua
ServerConfig.Access = function(source, tarp, action, cache)
    cache = cache or {}
    cache.keys = cache.keys or exports.ox_inventory:Search(source, 'slots', 'carkey') or {}
    for _, slot in pairs(cache.keys) do
        if slot.metadata and TarpUtil.NormalizePlate(slot.metadata.plate) == tarp.plate then
            return true
        end
    end
    return false
end
```

`cache.keys` makes one inventory search per player per sync, not one per tarp.
{% endstep %}

{% step %}
### Optional: instant refresh

By default (`AccessRecheck = true`) a given or lost key is noticed within 2 s. To react instantly and save the periodic re-check, add this to **one of your own server scripts**:

```lua
local function refresh(payload)
    for _, field in ipairs({ 'fromInventory', 'toInventory', 'inventoryId' }) do
        local player = tonumber(payload[field])
        if player then exports.vehicle_tarp:RefreshAccess(player) end
    end
end

local options = { itemFilter = { carkey = true } }
exports.ox_inventory:registerHook('swapItems', function(payload)
    SetTimeout(0, function() refresh(payload) end)
end, options)
exports.ox_inventory:registerHook('createItem', function(payload)
    SetTimeout(0, function() refresh(payload) end)
end, options)
```

Then, in `config/server.lua`:

```lua
ServerConfig.AccessRecheck = false
```
{% endstep %}
{% endstepper %}

## Owner **or** key

```lua
ServerConfig.Access = function(source, tarp, action, cache)
    cache = cache or {}
    if cache.identifier == nil then
        cache.identifier = TarpBridge.GetIdentifier(source) or false
    end
    if cache.identifier ~= false and cache.identifier == tarp.vehicleOwner then return true end

    cache.keys = cache.keys or exports.ox_inventory:Search(source, 'slots', 'carkey') or {}
    for _, slot in pairs(cache.keys) do
        if slot.metadata and TarpUtil.NormalizePlate(slot.metadata.plate) == tarp.plate then return true end
    end
    return false
end
```

{% hint style="info" %}
With the key-only rule, an owner who loses the key can no longer see their covered vehicle. Staff can still uncover it with `tarp_uncover <id>`.
{% endhint %}
