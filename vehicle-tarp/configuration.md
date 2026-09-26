---
description: config/shared.lua - interaction, display, props
icon: sliders
---

# Configuration

`config/shared.lua` is read by the server **and** the clients: language, keys, interaction and tarp display. Server-only options (access, vehicle table, automation) are in [Server configuration](server-configuration.md).

## Language and keys

| Option                     | Default                                | Meaning                                                                                                                                            |
| -------------------------- | -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Config.Locale`            | `'fr'`                                 | File of `locales/` to use. Also sets the command names. See [Languages](languages.md).                                                             |
| `Config.Keys`              | `{ cover = '', uncover = '' }`         | Default keys of the cover / uncover commands, e.g. `'F7'`. Empty = no key mapping. Players can rebind them in **Settings > Key Bindings > FiveM**. |
| `Config.StaffPanel`        | `{ command = 'tarp_panel', key = '' }` | Staff panel command and default key. `command = false` disables the panel. See [Staff panel](staff-panel.md).                                      |
| `Config.CommandCooldownMs` | `1000`                                 | Client cooldown of the cover command.                                                                                                              |

{% hint style="warning" %}
Renaming a command, or changing `Config.Locale`, resets the key players bound to it.
{% endhint %}

## Interaction

| Option                    | Default | Meaning                                                                                                                                                                                 |
| ------------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Config.InteractDistance` | `3.0`   | Metres. Distance to cover the nearest vehicle and to uncover a tarp.                                                                                                                    |
| `Config.Prompt`           | `'3d'`  | `'3d'` = text above the tarp, `'help'` = GTA help text (top left), `false` = no text and no key: use your own interaction script (see the [ox\_target example](examples/ox-target.md)). |
| `Config.InteractKey`      | `38`    | GTA control to uncover (`38` = `E`).                                                                                                                                                    |
| `Config.InteractKeyLabel` | `'E'`   | Label shown in the 3D text.                                                                                                                                                             |
| `Config.StatsKey`         | `23`    | GTA control that shows / hides the vehicle stats card (`23` = `F`). `false` disables the card.                                                                                          |
| `Config.StatsKeyLabel`    | `'F'`   | Label shown in the 3D text.                                                                                                                                                             |

The stats card looks like the GTA Online garage: top speed, acceleration, braking and traction of the **model**, against the best vehicle of its class. Mods are not counted.

## Display

| Option                  | Default                         | Meaning                                                                          |
| ----------------------- | ------------------------------- | -------------------------------------------------------------------------------- |
| `Config.RenderDistance` | `80.0`                          | Metres. Distance at which tarp props are created.                                |
| `Config.ScanIntervalMs` | `500`                           | How often props are created / deleted around the player.                         |
| `Config.TarpAlpha`      | `150`                           | Opacity of the prop, `0` (invisible) to `255` (opaque). Props have no collision. |
| `Config.TarpOffset`     | `{ x = 0.0, y = 0.0, z = 0.0 }` | Offset applied to every prop.                                                    |

## Tarp props

`Config.TarpModels` picks the prop in this order: **exact vehicle model**, then **vehicle class**, then **default**.

```lua
Config.TarpModels = {
    default = 'imp_prop_covered_vehicle_01a',
    vehicles = { cheetah = 'prop_cheetah_covered', jb700 = 'prop_jb700_covered' },
    classes = { [0] = 'imp_prop_covered_vehicle_07a', [7] = 'prop_cheetah_covered' },
}
```

* `vehicles`: vehicle model name = prop.
* `classes`: `GetVehicleClass` id = prop. Classes not listed use `default`.
* A prop missing from the game falls back to `default`. If `default` is missing too, a warning is shown once in the F8 console and the tarp can still be uncovered, without an object.

## Functions

### `Config.SetVehicleProperties(vehicle, props, format)`

Applies the customs to the recreated vehicle, on the client that owns it. The shipped function uses `qbx_core`, `qb-core` or `es_extended`, depending on what is started. Replace it if you use another props system.

### `Config.Notify(message, kind)`

Shows a notification. Ships with the native GTA feed. Replace it with your notification script:

```lua
Config.Notify = function(message, kind)
    exports.your_notify:Show(message, kind)
end
```
