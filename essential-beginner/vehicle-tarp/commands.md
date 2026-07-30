# Commands

All of these are gated by `Config.adminGroups` on `qbx_core`, or by the ACE principal `command.vtarp` on standalone and plain `qb-core`.

| Command           | Effect                                                                                                                                                |
| ----------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| `/tarp`           | Tarps the vehicle you are in, or the nearest tracked one if you are on foot. Does nothing when `enableTarp` is `false`.                               |
| `/untarp`         | Uncovers the nearest tracked vehicle within 50 m, bypassing `canAccess` and the distance check.                                                       |
| `/deltarp`        | Stops tracking the vehicle you are in, or the nearest within 50 m: removes the live entity and deletes its database row. Irreversible.                |
| `/listtarp`       | Lists every currently tarped vehicle - id, plate, owner, and how long ago it was tarped.                                                              |
| `/tptarp <id>`    | Teleports you to the saved position of that row id.                                                                                                   |
| `/showtarps`      | Toggles **admin reveal**: you see every tarp within `renderDistance` regardless of `canAccess`, and `[E]` uncovers them. Run it again to turn it off. |
| `/vtarp_track`    | Tracks the vehicle you are currently sitting in.                                                                                                      |
| `/vtarp_ui`       | Opens the staff map. `ESC` closes it.                                                                                                                 |
| `/vtarp_ui_debug` | Toggles the diagnostic overlay.                                                                                                                       |

{% hint style="info" %}
The admin reveal from `/showtarps` persists until you toggle it back off, and the permission behind it is re-checked on every refresh - a demotion closes the reveal without waiting for a reconnect.
{% endhint %}

## The staff map - `/vtarp_ui`

A full-screen map of Los Santos showing the tracked fleet, with a searchable sidebar. The command toggles it, `ESC` closes it. It opens on its own - `/vtarp_ui_debug` is not a prerequisite.

Gated by `ServerConfig.canUseDebugView` when you define it, by the admin check otherwise. **Losing the permission closes the map**, because it shows every plate, owner and coordinate on the server - not just the ones you could uncover.

* Search matches **plate and owner**, trimmed and case-insensitive.
* The three state chips are cumulative, and **no chip selected shows everything**.
* Markers are green for `ACTIVE`, blue for `TARPED`, and amber and square for an anomaly - the same colours as the diagnostic overlay, so the same vehicle reads the same on both.
* Positions are the **last known** ones, not live ones. A moving vehicle is drawn where it was last seen, and an expanded row shows how old its reading is.

Selecting a row expands it onto six actions:

| Action                | Effect                                                                                                                                                            |
| --------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Y aller`             | Teleports you to the vehicle's saved coordinates.                                                                                                                 |
| `Le faire venir`      | Brings the vehicle 3 m in front of you, at your own Z. A tarped vehicle stays tarped, its tarp simply moves. Refused if someone is inside. Asks for confirmation. |
| `Bacher` / `Debacher` | Tarps or uncovers the row, bypassing `canAccess` and the distance check. Only the one that applies to the row's state is shown.                                   |
| `Supprimer le suivi`  | Deletes the live entity **and** the database row. Irreversible, asks for confirmation.                                                                            |
| `Waypoint`            | Sets a GPS waypoint. Client-only - nothing is sent to the server and nothing is logged.                                                                           |

The five server-side actions are logged through `ServerConfig.onAdminAction`.

## The diagnostic overlay - `/vtarp_ui_debug`

Toggles, for the calling player only:

* a **blip** for every tracked vehicle, on the minimap and the full map;
* an **oriented 3D box** and a **floating label** around every tracked vehicle within `debugDrawDistance`;
* a **passive HUD** listing the fleet, with a countdown to tarping for active vehicles and an elapsed time for tarped ones. It never takes mouse focus, so the game stays playable.

{% hint style="warning" %}
This view **ignores `canAccess` and spawns nothing**. A tarp you have no access to shows a box and a label, but no prop. `/showtarps` is the command that actually renders the props to you.
{% endhint %}

It refuses the server console - there is no client to draw for.

Two situations are flagged in yellow, and sorted to the top of the HUD list:

| Flag        | Meaning                                                                                     |
| ----------- | ------------------------------------------------------------------------------------------- |
| `DUPLICATE` | A tarped row that still has a live entity - a prop would be drawn on top of a real vehicle. |
| `GHOST`     | An active row whose vehicle no longer exists in the world.                                  |

## Measurement tooling

`/vtarp_debug [plate]` and `/vtarp_probe [plate]` are development tooling, gated by their own resolver `ServerConfig.canUseDebug` rather than by the admin check. They repeatedly damage and repair the vehicle they inspect and append to `vehicle_tarp/debug.log`.

Return `false` from `canUseDebug` to close them on a production server.
