# EpogArmory

> **Inspect anyone on the server — no matter where they are.**

A WoW 3.3.5 addon for Project Ascension that lets you open an in-game paperdoll for any scanned player — even if they aren't in your group, aren't in interact range, or aren't even logged in. Every player running the addon contributes their inspects to a shared mesh, so the more people install it, the more of the server you can look up instantly.

![EpogArmory in-game inspect frame](docs/ingame-inspect.png)

<!-- TODO: screenshot of the in-game inspect frame goes here -->

## The main feature — inspect anyone, any time

Blizzard's built-in inspect is gated by:

- **Group membership** (party / raid only)
- **Interact range** (about 10 yards)
- **Line of sight** and other engine quirks

EpogArmory removes all three. Click the minimap button (or type `/epogarmory show`) to open the browser, search for any player who's been seen by the mesh, and their paperdoll pops up immediately — full gear, enchants, gems, talent spec, ilvl. Useful for:

- Gear-checking a PUG applicant before you invite them
- Spotting who's over-/under-geared in a raid without asking
- Settling "what trinket does X use?" mid-theorycraft
- Seeing what gear someone was wearing last week when they nailed a parse

The catch: a player only shows up once somebody in the mesh has inspected them. So running the addon yourself gates you into the mesh — you contribute your scans, everybody else's scans flow back to you.

## How data flows

```
 You inspect a groupmate
       ↓
 Addon packs gear + talents into a chunked payload
       ↓
 Broadcast on the addon-message channel to RAID/PARTY + GUILD
       ↓
 Every other installer reassembles and stores it locally
       ↓
 You can now open their paperdoll from /epogarmory — any time
       ↓
 Admin periodically uploads their SavedVariables → epoglogs.com/armory
```

Ten guildies installed means every gear scan any of them performs lands in all ten `SavedVariables` files, and the admin only has to upload one to push it to the public site.

## What it scans (and doesn't)

- **Inspects groupmates** in dungeons and raids, out of combat, level 60+, ≥10 slots equipped.
- **Self-scans** on gear changes so you always contribute your own current set.
- **Filters utility loadouts** — fishing poles, mount-speed trinkets, Chef's Hat. A bank-alt scan can't overwrite your real gear.
- **Requires a committed spec** — 0/0/0 freshly-dinged scans and ambiguous hybrid builds (no 31-point capstone AND <61 total points) are dropped. Only real gear sets get archived.
- **24-hour mesh cooldown** per player-GUID — the network won't re-scan a given player more than once per day, no matter how many groups they join.
- **Caches item names / quality / ilvl** via `GetItemInfo` so Ascension custom items still resolve correctly on the public site even when Wowhead doesn't know them.

## Install

1. Download the latest `EpogArmory-vX.Y.zip` from [Releases](https://github.com/Defcons/epogarmory-addon/releases).
2. Extract so the `EpogArmory` folder lands in:
   ```
   <WoW root>/Interface/AddOns/
   ```
   On the Ascension Launcher this is typically:
   ```
   Ascension Launcher/resources/epoch_live/Interface/AddOns/
   ```
3. Restart the game (or `/reload` if already running).
4. A new button appears on your minimap — click to open the browser. Or type `/epogarmory` in chat.

## Commands

- `/epogarmory` — toggle the browser frame
- `/epogarmory show <name>` — open a specific player's paperdoll directly
- `/epogarmory status` — scanner state + queue depth
- `/epogarmory instance on|off` — only scan in party/raid instances (default: on)
- `/epogarmory debug on|off` — verbose logging to chat

## Data stored on your machine

Two `SavedVariables` tables:

- **`EpogArmoryDB`** — `players[GUID] = { name, realm, class, level, spec, gear[], scanTime, zone, scannedBy }`
- **`EpogItemCacheDB`** — `[itemID] = { name, quality, itemLevel, icon, ts }` — the client's own `GetItemInfo` output per item anyone has equipped

Nothing leaves your computer except via the addon-message channel (same class of traffic as DBM, Recount, Details, etc.). No web requests.

## Credits

Built by **Defcon**.

## License

MIT.
