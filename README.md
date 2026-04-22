# EpogArmory

WoW 3.3.5 addon for Project Ascension that collects equipped-gear snapshots of your groupmates and raid members, then broadcasts them across a mesh of other EpogArmory installs. The admin of [epoglogs.com](https://epoglogs.com) periodically uploads the aggregated data, and the site surfaces it as a public paperdoll browser at [epoglogs.com/armory](https://epoglogs.com/armory).

## What it does

- **Inspects groupmates** in dungeons and raids (out of combat, level 60+, ≥10 slots equipped).
- **Self-scans** on gear changes so you always contribute your own set.
- **Broadcasts scans** to party/raid/guild over an addon-message channel so every other installer receives and stores them.
- **Filters utility loadouts** (fishing poles, mount-speed trinkets, chef's hat) so a bank-alt scan doesn't overwrite your real gear.
- **Requires a committed spec** — scans of 0/0/0 freshly-dinged characters and hybrid builds with <31 in any tree and <61 total points are rejected, so only real gear sets get archived.
- **24-hour mesh cooldown** per player-GUID — the network won't re-scan a given player more than once per day no matter how many groups they join.
- **Caches item names/quality/itemLevel** via `GetItemInfo` so Ascension custom items still resolve correctly on the site even when Wowhead doesn't know them.

## Install

1. Download the latest `EpogArmory-vX.Y.zip` from [Releases](https://github.com/Defcons/epogarmory-addon/releases).
2. Extract the `EpogArmory` folder into:
   ```
   <WoW root>/Interface/AddOns/
   ```
   (On the Ascension Launcher the path is typically `Ascension Launcher/resources/epoch_live/Interface/AddOns/`.)
3. Log in. The addon starts scanning automatically. `/epogarmory` in chat to see status.
4. When the admin asks for your data, grab:
   ```
   <WoW root>/WTF/Account/<ACCOUNT>/SavedVariables/EpogArmory.lua
   ```

## How scans flow

```
You inspect a groupmate
    ↓
Your addon packs gear + talents into a chunked payload
    ↓
Broadcast on addon-message channel to RAID/PARTY + GUILD
    ↓
Every other EpogArmory-equipped player reassembles + stores it locally
    ↓
One admin periodically uploads their SavedVariables file to epoglogs.com
    ↓
Site ingests, dedups, and publishes the paperdoll browser
```

So ten guildies with the addon installed means every gear scan any of them performs ends up in all ten SavedVariables files — the admin only has to upload one.

## Configuration

Commands (type in chat):

- `/epogarmory status` — scanner state + queue depth
- `/epogarmory instance on|off` — toggle the "only in party/raid instances" filter (default: on)
- `/epogarmory debug on|off` — verbose logging to chat

## Data stored in SavedVariables

Two tables are persisted between sessions:

- **`EpogArmoryDB`** — `players[GUID] = { name, realm, class, level, spec, gear[], scanTime, zone, scannedBy }`
- **`EpogItemCacheDB`** — `[itemID] = { name, quality, itemLevel, icon, ts }` — sourced from the client's own `GetItemInfo` on every scan

Nothing leaves your computer except via the addon-message channel (same channel class as DBM, Recount etc.).

## Credits

Built for [epoglogs.com](https://epoglogs.com) by David + Claude.

## License

MIT.
