# HeroSiege Offline Tool - S10

## 2.7.0 Vault redesign

- Catalog, container grid/list, and inspector workflow with official Hero Siege branding.
- Read-only grid selection and batch-placement previews share the save editor's placement rules.
- Expanded exact artwork mappings, cached downloads, cancellation/retry and coverage export.
- Existing item, character, quest-warning, backup and reviewed-save behavior is preserved.
- **Artwork is not yet complete.** See `VAULT_RELEASE.md` and the packaged `artwork-unavailable-2.7.json` for the exact remaining gaps and test limitations.

## 2.6.0 reliability update

- Saves now show a field-by-field review. Character and shared `shop.ini` changes are backed up together before replacement, with a recovery journal for interrupted multi-file commits. This is recoverable coordination, not filesystem-level atomicity across multiple files.
- Loading/reloading/closing protects both staged changes and text still being edited. Quest refresh reloads form values as well as counters.
- Restore Backup provides a dated browser, known-good pins, slot/checksum checks, and optional restoration of the associated files from a grouped save. Legacy backups are identified separately. Bulk destructive backup cleanup has been replaced with this browser.
- Quest actions show fixed staged targets and explicit verification limits. **39/40 Warrior Souls and 499/500 Soul Essence are experimental preparations, not verified NPC completion fixes.** Serialization tests do not establish that Orre will accept a quest.
- Tests report missing local fixtures as SKIPPED. The build also runs a hidden WPF smoke test with synthetic data; no test writes to live game saves. Version comes from `Directory.Build.props`.

A local, open-source Windows 11 x64 save-item and character editor for **Hero Siege Season 10**. Quest tools are pinned to the verified Steam `7.0.11.0` executable; existing item-editor support is retained for prior Season 10 save formats.

This version never attaches to Hero Siege, bypasses nothing, and does not modify the game executable. It edits offline `.hss` save files only while Hero Siege and Easy Anti-Cheat are closed.

## Features

- Embedded searchable catalog of 1,426 entries: Season 10 uniques, 65 collectibles/fragments, 74 crafting materials, separate potion and equippable flask/vial categories, 156 runtime-ID-verified relics, 44 keys, 40 runes, 56 gems, and 30 jewel entries (including the 15 Uncut Jewel variants).
- Create unique/Satanic/Heroic items, generic bases, materials, fragments, collectibles, and rune/gem/jewel stacks.
- Manual type, ID, weapon subtype, seed, and stack quantity fields, with safe automatic grid placement.
- Load `stash.hss` or any `inventory_order_N.hss` sidecar and select friendly tab names such as **Shared Stash 1**.
- Clone any existing save record exactly, including materials, bases, and undocumented special records.
- Change only an existing item's roll seed without changing its identity or stat types.
- Show a compact item card with authoritative stat lines and roll ranges before staging.
- Reserve complete item footprints, pack repeated additions into free space, and report when no contiguous space remains.
- Stage changes in memory before saving.
- Multi-select rows with Ctrl/Shift, or select all visible rows, for one confirmed bulk deletion.
- Batch-create up to 500 copies of one item with guaranteed distinct roll seeds; a live footprint/capacity tooltip shows exactly how many copies fit, and preflight rejection leaves no partial batch staged.
- Selecting the **Keys** category reveals **Add Every Key at Stack Amount**. It stages all 44 runtime-verified keys at once, applies the existing Stack amount value to every key, preflights 44 free positions, and leaves no partial batch if the selected tab cannot fit them.
- Reject loads and saves while Hero Siege or EAC is running.
- Reject a save if it changed on disk after loading.
- Create and hash-verify an automatic backup before every write.
- Write through a validated temporary file and roll back from backup on failure.
- Restore any backup for the loaded save; rollback first makes and verifies a safety copy of the current file.
- Start maximized, with the configuration column constrained to vertical scrolling only for small or highly scaled displays.
- **Give Character 1 of Every Relic** appears only when a valid `inventory_order_N.hss` is loaded and **Character Relics** is selected; it validates the complete 156-ID runtime relic set, adds only missing identities, preflights capacity, and stages atomically.
- Valid character inventory sidecars that predate relic-tab initialization expose a non-mutating virtual **Character Relics** destination; the real container is created only when a relic is staged.
- Valid character inventory sidecars expose **Character Materials** for the complete verified type-13/type-14 catalog. Selecting a material or fragment auto-targets that tab, and the editor blocks those records from ordinary inventory or stash grids.
- Load a numbered `herosiegeN.hss` character save in a dedicated progression screen and stage **all campaign waypoints through Inferno**. The operation changes only the 55 verified act/zone waypoint fields; it leaves inventory, quests, stats, level, current difficulty, and every unrelated field untouched.
- Open the native **Character Save Forge** with **Character Editor…**. The complete slot vault and character interface run inside the same `HSOfflineLootForge.exe` process as the item editor.
- Edit Name, Class, Level, Hero Level, Experience, Gold, Wormhole Level, Herbalism, Enchanting, Jewelcrafting, and Mining. Shared Gold/profession values continue to use the game's `shop.ini` storage.
- Use Undo Unsaved Changes, Save Character, Save As, and the character-backup browser. Save As creates only the numbered character file; it does not clone inventory/Ether sidecars or commit shared gold/professions.
- Use Unlock All Difficulties, Unlock All Charm Slots, selectable Ether Point totals, the complete per-skill Subskills editor, and Odyssey conversion.
- **Max All Active Skill Upgrades** fills `s1`–`s10` for every verified allocated subskill tree in the active loadout. It uses 222 verified Season 10 active-tree IDs and 93 exact non-default cap rows, creates only verified allocated trees when missing, preserves the selected mutually exclusive `s11`–`s14` major node, and leaves all other loadouts untouched.
- Stage the native Odyssey (`soloselffound`) flag without changing equipment, level, talents, quests, or inventory.
- Browse 101 quest-object definitions plus 310 objective entries for the pinned installed 7.0.11 build. Active objectives are shown first. Existing bindings and fixed targets are retained, but NPC completion is not verified for every binding. A matching executable hash establishes build identity, not correctness of every quest mapping. See `QUEST_VERIFICATION.md` before game validation.
- **Grant Next Soulforge Page** detects only active quests 476–481 and stages the exact page objective: objective 1 for pages 11, 12, 14, 15, and 16, and objective 2 for page 13. It does not start quests, turn them in, advance the chain, or grant rewards.
- Quest features fail closed when the installed `Hero_Siege.exe` does not match SHA-256 `2F644AD840C0EF6073CF30D454581AF75DF4D5BE8C06984CA21F9AB47A30E534`; other editor features remain available.

## Roll editing: important limitation

Version 2.7.2 also supports dragging an item within a shared stash tab. Moves
are staged, reject overlaps/unknown dimensions, and use **Review & Save** with
the same backups and game-running guard. Escape cancels a drag. Dragging across
tabs or in special character containers is not enabled.

Hero Siege stores numerical item rolls as one seed (`a`). It does not persist each displayed stat line as an independent value. Changing the seed rerolls the numerical lines while preserving what the item does, but multiple lines cannot be directly assigned separate numbers without reimplementing the game's internal roll algorithm.

Natural socket counts also lack a verified independent editing path in this
tool. This is a save-format/calculation verification limitation, not an EAC
workaround requirement. The earlier RNG experiments did not establish exact
parity; no unsupported per-stat or socket fields are written.

The editor therefore supports:

- manual roll-seed entry;
- random roll seeds;
- cloning a known-good item and retaining or changing its seed.

The preview shows every definition available in the Season 10 data and its authoritative range. Exact numeric seed output is not claimed: the installed game computes those values inside item-specific branches of its compiled `GetItemSeed`/`GetItemStats` runtime. The editor deliberately displays ranges instead of inventing numbers that could disagree in-game.

## Safe usage

1. Exit Hero Siege completely and confirm EAC has also stopped.
2. Run `HSOfflineLootForge.exe`.
3. Select **Open Default Stash**, or choose a specific `.hss` file.
4. Select a friendly target such as **Shared Stash 1**, **Character Materials**, **Character Relics**, **Character Keys**, or **Character Runes / Gems**.
5. Select a catalog item; its correct format and full grid footprint are chosen automatically.
6. Select **Add Item to Selected Tab**.
7. Review the staged entries and select **Save Changes**.
8. Note the verified backup path shown at the bottom of the window.
9. Launch Hero Siege and test the result offline.

For waypoints, choose the target character's `herosiegeN.hss`. The app identifies the character by name, reports how many of the 55 verified fields already reach Inferno, and shows the character-only waypoint action. Stage the action and then select **Save Changes**. Lower difficulties are implied by waypoint level 4; the app does not change the character's currently selected difficulty.

For skill upgrades, load the character, confirm the displayed active talent loadout, and choose **Max All Active Skill Upgrades**. Only the ten ordinary nodes in verified, allocated active skills are maxed to their individual game-derived caps. Existing major-node choices are preserved because those four nodes are mutually exclusive. Like every character action, this remains staged until **Save Changes** creates a verified backup and commits it.

The main window's **Character Editor…** button opens the native character vault inside `HSOfflineLootForge.exe`. It includes the editable character/shop fields, save-as, rollback-safe saving, waypoint and difficulty unlocks, charm slots, Ether point presets, per-node active subskill editing, max-all subskills, Odyssey conversion, and backup management in the same process as the item editor. The waypoint action uses the Jebooty-validated all-difficulty transformation. Save writes are blocked while Hero Siege or Easy Anti-Cheat is running.

The Character Editor also contains **Quest Requirements / Soulforge**. Its catalog is loaded only after the installed game executable, 101-row quest-object table, and 310-row exact-objective table pass the 7.0.11 fingerprint checks. The default filter lists every translated objective encoded in the selected character's active quest slots. A grantable entry fills **Exact target** automatically from pinned compiled data; **Grant Selected** can also repair an overfilled known counter back to that exact value. Select **Book of Soulforge page** or use **Grant Next Soulforge Page** for the page chain. Every action requires exactly one matching active quest slot and valid objective field, stages only that exact counter, and uses the ordinary **Save Character** backup/atomic-write path. Unknown completion values, scene objects, and aliases without independent save records remain informational and cannot write arbitrary progress.

There is intentionally no direct Magic Find field. Hero Siege 7.0.11 calculates Magic Find at runtime from equipment, relics, talents, buffs, difficulty, and similar sources; the inspected character and sidecar saves do not contain a standalone persistent Magic Find value.

For relics, open the matching `inventory_order_N.hss`, select **Character Relics**, and choose an entry from the **Relics** category. Relics automatically use the genuine type-16 relic schema, switch to Character Relics when available, and are blocked from every stash/inventory container other than `inventory_relic_tab`. Independent current-game extraction confirms that type 16 contains exactly 156 runtime relic identities; the extra localization rows are not additional runtime IDs. For keys, select **Character Keys** and choose an entry from **Keys**. For runes, gems, and jewels, select **Character Runes / Gems** and choose the item from its catalog category. For an undocumented special item, use **Clone Selected** on a genuine existing record; this preserves every item-specific field.

For crafting materials and fragments, open the character's matching `inventory_order_N.hss` and choose an item from **Materials & Crafting** or **Fragments & Collectibles**. The editor automatically selects **Character Materials** and uses the verified native type-13/type-14 stack schema. Quantity `1` creates a native single record; larger quantities create one stack with the requested amount. This includes Gypsy's Prophecy (type 14, ID 63), Gypsy´s Fragment (type 14, ID 72), Satanic Crystal and its fragment, Prophet's Wisdom, Destiny Shard and its fragment, Blacksmith's Mallet and its fragment, Angel's Wisdom, Dice Fragment, and every other current catalog definition.

To create every key at once, select **Keys** in the category dropdown, set **Stack amount** to the quantity wanted for each key, choose the destination tab, and click **Add Every Key at Stack Amount**. The action validates the complete 44-ID key catalog and available space before staging anything. Saving remains a separate confirmed action with an automatic verified backup.

## Schemas

- **Unique**: proven unique/Satanic/Heroic structure (`w,j,m,b,a,c=1`).
- **Generic**: normal base structure (`j,b,a,c=0`).
- **Rune, gem, or jewel stack**: socketable stack structure (`w,j,o,b,a,c=0`).
- **Material / fragment / collectible stack**: native material structure (`j,b,a,c=0`, plus `o` only when quantity is greater than one).
- **Clone**: exact deep copy of a selected record, with a new key, position, and optional seed/quantity.

Manual IDs are an expert feature. The editor validates JSON, file integrity, positions, backups, and process state, but it cannot prove that an arbitrary undocumented type/ID combination exists in the installed game.

## Backups

Every save creates a timestamped backup beside the game saves:

`%LOCALAPPDATA%\Hero_Siege\hs2saves\LootForgeBackups`

To restore, close the game, copy the desired `.bak` file over its original `.hss` filename, and retain another copy of the current file first.

## Build

Requirements: Windows 11 and the .NET 8 SDK or newer.

```powershell
dotnet restore HsOfflineLootForge.sln --configfile NuGet.Config
dotnet build HsOfflineLootForge.sln -c Release --no-restore
dotnet run --project tests\HsOfflineLootForge.Tests -c Release --no-build
.\build.ps1
```

`build.ps1` produces a self-contained portable x64 ZIP, a source ZIP, and SHA-256 checksums.

This project is not affiliated with Panic Art Studios or Hero Siege. Use it only with offline saves you own.

See `THIRD_PARTY_NOTICES.md` for the notice retained from the supplied Hero Siege Save Editor reference implementation.
