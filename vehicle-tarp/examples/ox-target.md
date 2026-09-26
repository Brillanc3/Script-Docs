---
description: Uncover with the eye instead of the E key
icon: bullseye
---

# ox\_target

**What you get:** the E text disappears; players aim at the tarp with ox\_target and pick "Uncover" or "Stats".

{% stepper %}
{% step %}
### Disable the built-in prompt

In `config/shared.lua`:

```lua
Config.Prompt = false
```
{% endstep %}

{% step %}
### Add the target options

In **one of your own client scripts**. The model list must match `Config.TarpModels`:

```lua
local tarpModels = {
    'imp_prop_covered_vehicle_01a', 'imp_prop_covered_vehicle_02a', 'imp_prop_covered_vehicle_03a',
    'imp_prop_covered_vehicle_04a', 'imp_prop_covered_vehicle_05a', 'imp_prop_covered_vehicle_06a',
    'imp_prop_covered_vehicle_07a', 'prop_cheetah_covered', 'prop_jb700_covered',
    'prop_ztype_covered', 'prop_entityxf_covered',
}

local function nearestTarp()
    return exports.vehicle_tarp:GetNearestTarp()
end

exports.ox_target:addModel(tarpModels, {
    {
        name = 'vehicle_tarp_uncover',
        icon = 'fa-solid fa-car',
        label = 'Uncover',
        canInteract = function() return nearestTarp() ~= nil end,
        onSelect = function()
            local tarp = nearestTarp()
            if tarp then TriggerEvent('vehicle_tarp:client:uncover', tarp.id) end
        end,
    },
    {
        name = 'vehicle_tarp_stats',
        icon = 'fa-solid fa-gauge',
        label = 'Stats',
        canInteract = function() return nearestTarp() ~= nil end,
        onSelect = function()
            local tarp = nearestTarp()
            if tarp then TriggerEvent('vehicle_tarp:client:stats', tarp.id) end
        end,
    },
})
```
{% endstep %}
{% endstepper %}

{% hint style="info" %}
The server still checks distance, access and the uncover delay: the target only replaces the key press.
{% endhint %}
