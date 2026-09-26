---
description: The counterpart of Track
icon: link-slash
---

# Untracking a vehicle

`vehicle_tarp` never despawns vehicles. `Untrack` is a tool: it drops the row and never calls `DeleteEntity`. Deciding whether the entity should then be deleted is yours.

## The export

```lua
-- server-side, framework-agnostic
exports.vehicle_tarp:Untrack({ plate = plate })
```

Untracking a vehicle removes its row, so it will never be tarped by the idle sweep, never re-rendered as a prop, and never respawned at boot. Any tarp currently shown for it is hidden immediately.

Returns the number of rows untracked, so `0` tells you nothing matched.

## The filter

The filter is a table. Every field you provide is ANDed:

| Field   | Type      | Matches                                                                 |
| ------- | --------- | ----------------------------------------------------------------------- |
| `id`    | `integer` | the row id, as `Track` returned it                                      |
| `netId` | `integer` | a live entity, by its `vtarpId` statebag, falling back to its plate     |
| `plate` | `string`  | the plate, normalised on both sides - padding and case are irrelevant   |
| `owner` | `string`  | the owner recorded at `Track` time, as the framework bridge resolved it |

```lua
-- qbx / ESX, entity still alive when the garage stores it
exports.vehicle_tarp:Untrack({ netId = netId })

-- standalone, entity already deleted, only the plate is left
exports.vehicle_tarp:Untrack({ plate = plate })

-- disconnect, wipe, ban: the player's whole tracked fleet
exports.vehicle_tarp:Untrack({ owner = identifier })

-- one specific vehicle of that player
exports.vehicle_tarp:Untrack({ owner = identifier, plate = plate })
```

## What it does to the entity

{% hint style="info" %}
The entity is **never deleted**. `Untrack` drops the database row and hands the vehicle back to you exactly as it was - deleting it, storing it or leaving it parked is your call.
{% endhint %}

On the entity itself it clears the two statebags `Track` sets - `vtarpId`, which is the only thing linking the entity to a row, and `persisted`, the anti-cleanup flag - so the vehicle goes back to being an ordinary untracked entity. Two caveats, in opposite directions:

* `persisted` is cleared unconditionally. If another resource had already set it before `Track`, `Untrack` clears a flag `vehicle_tarp` never owned.
* Clearing it needs the entity, and outside of `Untrack({ netId = ... })` the entity is found through the `vtarpId` statebag. A `restart vehicle_tarp` loses that statebag, so an `Untrack` by plate, `id` or `owner` after such a restart drops the row and leaves `persisted` set on the vehicle until the next full server restart. **Pass `netId` when you have it** and this cannot happen.

## It raises

{% hint style="warning" %}
`Untrack` raises on an empty filter, on a non-table, and on any unknown key - **even alongside a valid one**. `Untrack({ plate = p, ownerId = o })` fails loudly rather than silently untracking on the plate alone while ignoring a filter you believed was active.
{% endhint %}

Returning `0` there would let a typo pass for a no-op, and treating an empty filter as "everything" would wipe the fleet.

## It yields

`Store.delete` awaits its MySQL `DELETE` before dropping the KVP key, deliberately - deferring it would make the deletion resurrectable at the next boot. Call `Untrack` from a context that can yield, or wrap it in a `CreateThread` on your side.

## Rows it skips

A row is skipped - and left out of the count - while an untarp spawn is in flight for it: the entity exists but is not linked to the row yet, so untracking there would leave an orphan carrying `persisted`. That window is under a second; retry.

## Not the same as `/deltarp`

`Untrack` is the counterpart of [`Track`](tracking-a-vehicle.md), not of `/deltarp`. That admin command deletes the entity as well - see [Commands](commands.md).
