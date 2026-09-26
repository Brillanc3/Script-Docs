---
description: Vehicle creation with metadata and carkey items
icon: car-side
---

# mVehicle

**What you get:** [mVehicle](https://github.com/Mono-94/mVehicle) creates and deletes the vehicle itself (metadata kept), and its `carkey` items give access to the tarp.

{% stepper %}
{% step %}
### Disable mVehicle persistence

In mVehicle's config:

```lua
Config.Persistent = false
```

Otherwise mVehicle respawns covered vehicles on restart, next to their tarp.
{% endstep %}

{% step %}
### Create the vehicle with mVehicle

```lua
ServerConfig.CreateVehicle = function(data)
    local Vehicles = exports.mVehicle:vehicle()
    local dbVehicle = Vehicles.GetVehicleByPlate(data.plate, true)
    if not dbVehicle then return nil end
    dbVehicle.coords = data.coords
    local created = Vehicles.CreateVehicle(dbVehicle)
    return created and created.entity
end
```
{% endstep %}

{% step %}
### Delete the vehicle with mVehicle

```lua
ServerConfig.DeleteVehicle = function(vehicle, record)
    exports.mVehicle:vehicle().DeleteFromTable(vehicle, true)
end
```

Without it, mVehicle keeps an entry for an entity that no longer exists.
{% endstep %}

{% step %}
### Access: the `carkey` item

mVehicle keys are `carkey` items with the plate in their metadata. Use the rule of the [ox\_inventory key item](ox-inventory.md) page as is.
{% endstep %}
{% endstepper %}

{% hint style="info" %}
`OnVehicleRecreated` gives no key: the `carkey` item stays in the inventory while the vehicle is covered. Giving a key there would create keys for staff who uncover.
{% endhint %}
