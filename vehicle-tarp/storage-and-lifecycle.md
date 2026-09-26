---
description: Database, damage, auto re-cover, restarts
icon: database
---

# Storage and lifecycle

## Database

The table `vehicle_tarp` is created automatically by `oxmysql`: one row per covered vehicle (`vehicle_id`, plate, model, position, bucket, owners, date, lock). Nothing to import.

* Customs are **not** copied: they are read back from the vehicle table on uncover.
* If `oxmysql` is not available at start, covering and uncovering are **disabled** (red line in the console). No vehicle is ever deleted without a saved record.
* `ServerConfig.Persistence.ttlDays = 30`: a tarp not uncovered for 30 days is deleted on start.

{% hint style="info" %}
**Upgrading from an older version:** if the `vehicle_tarp` table misses expected columns, it is renamed `vehicle_tarp_legacy_<timestamp>` (kept as a backup) and vehicles still covered by the old version become normal vehicles again.
{% endhint %}

## Damage

On cover, the damage is merged into the vehicle table row: body, engine and tank health, dirt, burst tyres, broken windows. The vehicle comes back in the same state.

* Detached doors and wheels cannot be read by the server and are not saved.
* If a garage or mechanic changed the row at the same moment, nothing is written.
* QBCore / ESX: check that your version uses the same damage keys (`tireBurstState`, `windowStatus` / `tyreBurst`, `windowsBroken`).

## Auto re-cover

A **tracked** vehicle - uncovered by the resource, or passed to the `Track` export - left unused for `idleMinutes` (30) is covered again, unless:

* someone is inside;
* it moved since the last check (30 s);
* a player is closer than `playerRadius` (15 m).

Configure it in `ServerConfig.AutoRecover`. The tracking list is in memory: after a reboot, a vehicle comes back only if your garage calls `Track`.

## Cover on restart

`ServerConfig.CoverOnShutdown = true`: on a **txAdmin** restart or stop (scheduled or from the panel), every registered vehicle still out is covered where it stands - without uncover delay, even with someone inside. After the reboot, the owner uncovers it right away.

{% hint style="warning" %}
A crash, or a stop outside txAdmin, covers nothing. Those vehicles follow your garage rules (e.g. impound).
{% endhint %}
