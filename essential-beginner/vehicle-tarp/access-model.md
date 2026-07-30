---
description: Who can see a tarp, and who can uncover it
---

# Access model

One function decides both: `ServerConfig.canAccess`, in `config/server.lua`.

{% hint style="danger" %}
`config/server.lua` is server-only. It is listed in `server_scripts` and must **never** appear in `files{}` or `shared_scripts` - a client would be able to fetch it.
{% endhint %}

## `canAccess`

```lua
ServerConfig = {
    renderDistance = 30.0,

    ---@param source number  player server id
    ---@param vehicle { id:integer, plate:string, model:string|number, owner:string?, vehicleId:integer?, coords:vector4? }
    ---@return boolean
    canAccess = function(source, vehicle)
        return false
    end,
}
```

Return `true` if `source` may **see** the tarp prop and **uncover** it with `[E]`. It runs server-side and is authoritative: an untarp request is re-validated through it, even though the client only shows the prompt after its own poll already passed.

### The `vehicle` argument

| Field       | Meaning                                                                                    |
| ----------- | ------------------------------------------------------------------------------------------ |
| `id`        | vehicle\_tarp's own row id                                                                 |
| `plate`     | The raw plate from `GetVehicleNumberPlateText` - **space-padded to 8 characters**          |
| `model`     | The vehicle model                                                                          |
| `owner`     | The identity recorded when the vehicle was tracked (`citizenid`, or `license:` standalone) |
| `vehicleId` | The `player_vehicles` id, or `nil` if the vehicle is not owned there                       |
| `coords`    | The last saved position                                                                    |

{% hint style="warning" %}
**A tarped vehicle has no entity.** It was deleted server-side, which is the whole point of tarping. Any check that needs the live vehicle - session keys, lockpick key state, anything reading the entity or one of its statebags - can never answer here, and would make every tarp permanently invisible.

Answer from `vehicle`'s fields only: an ownership record, or your key script's own lookup by `plate` or `vehicleId`.
{% endhint %}

### Example: owner of the `player_vehicles` record

```lua
canAccess = function(source, vehicle)
    local player = exports.qbx_core:GetPlayer(source)
    if not player then return false end

    if vehicle.vehicleId and GetResourceState('qbx_vehicles') == 'started' then
        local pv = exports.qbx_vehicles:GetPlayerVehicle(vehicle.vehicleId)
        if pv and pv.citizenid == player.PlayerData.citizenid then return true end
    end

    return false
end
```

### Example: a key item carrying the plate

An inventory item whose metadata holds the plate resolves fine with no entity spawned, so it is a good fit here.

Mind the trim: the tarp row keeps the raw, space-padded plate, while most key scripts store it trimmed.

```lua
canAccess = function(source, vehicle)
    local plate = type(vehicle.plate) == 'string' and vehicle.plate:match('^%s*(.-)%s*$')
    if not plate or plate == '' then return false end

    local count = exports.ox_inventory:Search(source, 'count', 'car_key', { plate = plate })
    return type(count) == 'number' and count > 0
end
```

The two can of course be combined - return `true` on either.

## `renderDistance`

Metres at which an allowed player starts seeing a tarp prop. Default `30.0`.

It is **not** the same thing as `Config.untarpDistance` (`3.0` by default), the much shorter range at which the `[E]` prompt appears. Seeing a tarp from across the street and standing close enough to uncover it are two separate distances.

## Reacting to a key changing hands

`Config.accessRefreshItems`, in `config/shared.lua`, lists the inventory items your `canAccess` reads:

```lua
accessRefreshItems = { car_key = true },
```

An item count changing fires no tarp event, so without this list a player who has just been handed a key would only see the tarp on the next reconcile poll - up to `renderReconcileSeconds` late. Listed here, their client re-syncs the instant the count moves.

Requires `ox_inventory`. Leave it empty to rely on the poll alone.

## Logging admin actions

`ServerConfig.onAdminAction` is called after every action launched from the `/vtarp_ui` map: `teleportTo`, `bringHere`, `forceTarp`, `forceUntarp` and `deleteTracking`. The GPS waypoint is client-only and never reaches it.

```lua
---@param entry { action:string, source:number, name:string, identifier:string?,
---              vehicleId:integer, plate:string, owner:string?, state:string,
---              coords:{x:number,y:number,z:number}?, ok:boolean }
onAdminAction = function(entry) ... end
```

* It ships as a one-line `print`, so actions are traceable in the txAdmin log from the moment you install the resource.
* Set it to `false` to log nothing at all.
* It is called **after** the action and carries an `ok` flag: a refused action is a trace worth keeping too.
* It is wrapped in a `pcall`, so it cannot break the action or the player's notification. Do not rely on throwing here to cancel anything - the action has already happened, and you would simply lose the trace.

## Gating the diagnostic views

Two more resolvers sit in the same file, both defaulting to your admin check:

| Resolver                  | Gates                                                      |
| ------------------------- | ---------------------------------------------------------- |
| `canUseDebugView(source)` | `/vtarp_ui` (the staff map) and `/vtarp_ui_debug`          |
| `canUseDebug(source)`     | `/vtarp_debug` and `/vtarp_probe`, the measurement tooling |

They are deliberately separate. `canUseDebug` guards commands that repeatedly damage and repair the vehicle they inspect and write to `debug.log` - return `false` from it on a production server. `canUseDebugView` guards views that change nothing, but that show every plate, owner and coordinate on the server.

`canUseDebugView` is re-evaluated on **every** refresh, not only when the command is run: a watcher who loses the permission stops receiving the feed and has their view closed, without waiting for a reconnect.
