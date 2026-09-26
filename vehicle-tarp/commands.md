---
description: Player commands, keys, staff and debug commands
icon: terminal
---

# Commands

## Players

Command names depend on the language (`Config.Locale`).

| `en`      | `fr`        | Effect                      |
| --------- | ----------- | --------------------------- |
| `/tarp`   | `/bacher`   | Covers the nearest vehicle. |
| `/untarp` | `/debacher` | Uncovers the nearest tarp.  |

### Near a tarp

Within `Config.InteractDistance` (3 m), on foot:

| Key | Effect                              |
| --- | ----------------------------------- |
| `E` | Uncover ("Uncover vehicle ID #12"). |
| `F` | Show / hide the vehicle stats card. |

Keys and text are set by `Config.Prompt`, `Config.InteractKey` and `Config.StatsKey`. Default keys for the two commands: `Config.Keys` (rebindable in **Settings > Key Bindings > FiveM**).

### Conditions

{% tabs %}
{% tab title="Cover" %}
* The player is on foot, within 3 m of the vehicle.
* The vehicle is **empty** and **stopped**.
* The vehicle is in the **vehicle table** (same plate, same model).
* `Access` returns `true` for `cover` (if `CoverRequiresAccess`).
{% endtab %}

{% tab title="Uncover" %}
* The player is within 3 m of the tarp, in the same routing bucket.
* `Access` returns `true` for `uncover`.
* The uncover delay has expired (players only).
* If a vehicle with the same plate is already in the world, no duplicate is created: the tarp is simply removed.
{% endtab %}
{% endtabs %}

## Staff

Requires the ace `vehicle_tarp.admin`. Names are set in `ServerConfig.Staff.commands`.

| Command             | Effect                                                                                      | Console |
| ------------------- | ------------------------------------------------------------------------------------------- | ------- |
| `tarp_view`         | Shows / hides **every** tarp for you. Off on each connection.                               | No      |
| `tarp_uncover <id>` | Uncovers remotely: no key, no delay.                                                        | Yes     |
| `tarp_cover <id>`   | Covers the vehicle with this id if it is out, wherever it is. It must be empty and stopped. | Yes     |
| `tarp_panel`        | Opens the [staff panel](staff-panel.md).                                                    | No      |

## Diagnostics

Requires the ace `command.tarp_debug` (name: `ServerConfig.DebugCommand`).

| Command                             | Effect                                                         |
| ----------------------------------- | -------------------------------------------------------------- |
| `tarp_debug whoami [playerId]`      | Framework, license and identifier of a player.                 |
| `tarp_debug list`                   | Every tarp with its position, owner, date and lock.            |
| `tarp_debug forget <id>`            | Deletes a tarp record without recreating the vehicle.          |
| `tarp_debug idle <plate>`           | Marks a tracked vehicle as idle: re-covered at the next check. |
| `tarp_debug access <playerId> <id>` | Result of `Access` (`view`) for this player and tarp.          |
