---
description: From download to the first covered vehicle
icon: download
---

# Installation

{% stepper %}
{% step %}
### Put the resource in place

Copy the `vehicle_tarp` folder into your resources, for example `resources/[standalone]/vehicle_tarp`.
{% endstep %}

{% step %}
### Edit `server.cfg`

Start `vehicle_tarp` **after** `oxmysql` and your framework, then give the staff permissions:

```
ensure oxmysql
ensure qbx_core        # or qb-core / es_extended, if you use one
ensure vehicle_tarp

add_ace group.admin vehicle_tarp.admin allow
add_ace group.admin command.tarp_debug allow
```

| Ace                  | Grants                                                                                                           |
| -------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `vehicle_tarp.admin` | Staff commands, staff panel, covering without the uncover delay. It does **not** grant access to players' tarps. |
| `command.tarp_debug` | The diagnostics command `tarp_debug`.                                                                            |

{% hint style="info" %}
If `[standalone]` is already started by an `ensure [standalone]` line, that line is enough - as long as `oxmysql` and your framework start before it.
{% endhint %}
{% endstep %}

{% step %}
### Pick the language

In `config/shared.lua`:

```lua
Config.Locale = 'en' -- 'en' = /tarp and /untarp, 'fr' = /bacher and /debacher
```

The language also sets the **command names**. See [Languages](languages.md).
{% endstep %}

{% step %}
### Decide who has access

Open `config/server.lua` and write `ServerConfig.Access`. The simplest rule, which works on every framework, is "the owner of the vehicle":

```lua
ServerConfig.Access = function(source, tarp, action, cache)
    cache = cache or {}
    if cache.identifier == nil then
        cache.identifier = TarpBridge.GetIdentifier(source) or false
    end
    return cache.identifier ~= false and cache.identifier == tarp.vehicleOwner
end
```

Using key items instead? See [Access](access-model.md) and the [Examples](examples/).

{% hint style="warning" %}
If `Access` never returns `true`, nobody can cover, see or uncover a tarp. Staff still can, with the staff commands.
{% endhint %}
{% endstep %}

{% step %}
### Start and check

Start the server and read the console:

```
[vehicle_tarp] 0 tarp(s) loaded, 0 purged
```

A **red** line means `oxmysql` or the vehicle table is not usable: covering is disabled until it is fixed. In game, as admin:

```
tarp_debug whoami
```

It prints the detected framework and your identifier. Then stand next to one of your registered vehicles and type `/tarp`.
{% endstep %}

{% step %}
### Optional: connect your garage

Call `exports.vehicle_tarp:Track(netId, playerId)` when your garage spawns a vehicle, so it is re-covered after inactivity and appears in the staff panel. See [Exports and events](exports-and-events.md).
{% endstep %}
{% endstepper %}

## When to restart what

| You changed                                 | Needed                                 |
| ------------------------------------------- | -------------------------------------- |
| `config/*.lua`, `locales/*.lua`             | `restart vehicle_tarp`                 |
| A file added or renamed in `fxmanifest.lua` | `refresh`, then `restart vehicle_tarp` |

{% hint style="info" %}
The resource can be encrypted with Asset Escrow: `config/*.lua` and `locales/*.lua` are in `escrow_ignore`, so they stay readable and editable.
{% endhint %}
