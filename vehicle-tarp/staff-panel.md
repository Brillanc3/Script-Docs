---
description: Map of every tarp and every tracked vehicle
icon: map
---

# Staff panel

`/tarp_panel` opens a full-screen panel for staff (ace `vehicle_tarp.admin`). `Escape` closes it.

```lua
-- config/shared.lua
Config.StaffPanel = { command = 'tarp_panel', key = '' } -- key = 'F10' for a default key
```

`command = false` disables the panel.

## What it shows

* A map of Los Santos (atlas, road or satellite) with:
  * every **tarp** in orange, red while the player uncover delay runs;
  * every **tracked vehicle out** in blue: vehicles recreated by an uncover, or passed to the `Track` export.
* Counters: covered, out, locked.
* A search by ID, plate or model, and filters covered / out.

Data refreshes every 5 seconds while the panel is open.

## Actions

| Action  | Effect                                              |
| ------- | --------------------------------------------------- |
| Uncover | Uncovers the tarp: no key, no delay.                |
| Cover   | Covers a vehicle out. It must be empty and stopped. |
| Go to   | Teleports you to the vehicle or tarp.               |
| Bring   | Moves it 3 m in front of you.                       |
| Send    | Moves it 3 m in front of another player.            |

* Moving a **tarp** moves its record: lock and owners are kept.
* Moving a **vehicle out** is refused if someone is inside.
* Both take the routing bucket of the target player.

{% hint style="info" %}
Every request is checked against the ace on the server. A player who is not staff gets "You are not allowed to do this".
{% endhint %}
