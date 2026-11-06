# Track folders and level scripts

A track folder is the unit exported by Track Studio and uploaded to Workshop. It can contain the level's scripts and native world data as well as meshes, materials, textures and race definitions. Export preserves these files; converting painted vegetation to static props is optional.

Expanded level content requires a matching game update. Track Studio checks the `FO_TRACK_LEVEL_CONTENT_1` capability before installing an export that uses it. Exporting alone does not update the game. Track Studio never patches the installed game. Use a game build that supports the required features; authoring and complete folder export remain available when installation needs an update.

## Multiple events in one track

Exports preserve all active FlatOut race, arena and stunt setups in the level. A stock environment's forward/reverse routes and arenas, or a stadium's individual stunts, stay in one folder and Workshop item. The game presents each event as a separate menu entry, using the original English event name when available. Frozen events and events below a frozen parent are left out.

Expanded events require the `FO_TRACK_EVENTS_1` game capability. Track Studio checks it before replacing an installed folder. **Test in game** uses a compatible mode for the installed event instead of always requesting a lap race.

A basic single race can still be installed on a game with the original track-folder support. Track Studio writes a legacy manifest only in the installed copy, preserving its lap/time-trial/carnage modes, nonnegative defaults, explicit day/time presets, dirt and minimap settings. Exported folders retain the complete event format. Multiple events, other modes, weather overrides, layer selectors, bomb tuning, inherited mode defaults or an empty time list require expanded event support; installation refuses them without replacing the previous track. Level scripts/data and port animation/shaders also require a matching runtime and cannot be silently omitted for compatibility.

The new terrain template includes empty vegetation sidecars for editing and baking. When the older game does not support them, installation omits only the template's exact unused empty files and their preload entries from its private copy. Both the current scene and the published level must have no vegetation spawner or explicit GIS reference. Painted vegetation, changed or unreadable sidecars and referenced files require the expanded runtime. Workspace files and complete exports remain unchanged.

The `eventFormat: 1` extension in `track.json` stores the primary event and a flat `variants` array for the others. Each event has its own `eventId`, actor name, supported modes, defaults, day/time/weather presets, layer selection, minimap and bomb-mode tuning. Variants share the package id, level namespace and test/release status. They cannot redirect their files to another track or nest more variants.

For copied stock levels, these settings come from the matching `Levels.cfg` registration of the original level; a registration for the copy takes precedence. This does not edit the shared registration file. New unregistered races get lap/time-trial modes, new arenas get survivor/deathmatch modes, and stunts get the stunt mode. Imported games' own race controllers must first be converted to FlatOut setups.

An empty day/time list preserves the level's authored time. It does not force noon. Missing bomb coefficients, incompatible mode types, duplicate active actor names and malformed settings produce export errors instead of becoming a lap race silently.

## Folder layout

`MY_TRACK` below is the level name in `track.json`. Keep every custom file inside its owning track's folders.

```text
track.json
WIN32/LEVELS/MY_TRACK/MY_TRACK.PLM
WIN32/PACLOG/LEVELS/MY_TRACK/MY_TRACK.PLM.PACLOG
WIN32/LEVELS/MY_TRACK/level.lua
WIN32/LEVELS/MY_TRACK/scripts/...
WIN32/LEVELS/MY_TRACK/data/...
WIN32/GRAPH/MODS/MY_TRACK/...
WIN32/GRAPH/TRACKS/MY_TRACK/...
WIN32/TEXTURES/PROBES/MY_TRACK/...
Common/LEVELS/MY_TRACK/...
UI/MY_TRACK/...
```

Under either owning `LEVELS/MY_TRACK/` folder, exports support:

| Files | Purpose |
| --- | --- |
| `.lua`, `.cfg` | Game scripts and configuration |
| `.json`, `.csv`, `.txt` | Track-owned data; scripts must explicitly read it |
| `.navmesh`, `.ssb`, `.pws` | Native navigation, baked splines and streamed world data |
| `.gis` | Native painted vegetation instances |
| `.tga` | Terrain colour maps |
| `.paf`, `.bank` | Native audio data; inclusion does not automatically register or play it |

These supplement the existing level, terrain and geometry formats. Compiled shaders (`.pfx`) and animation clips (`.pan`) belong in the track's `WIN32/GRAPH/MODS/MY_TRACK/` or `WIN32/GRAPH/TRACKS/MY_TRACK/` folders. Shared engine/configuration overrides, another track's files, native DLLs and arbitrary PKG archives are not part of this format.

Published XML contains the complete level, including recovered tile actors. Run development game tests with `Engine.UseStreaming = false`, as shipping games do, to avoid loading those actors twice. Keeping PWS files in the folder does not switch this publication format to streaming.

For a retail workspace, add track-owned content under `Workspace/Common/LEVELS/MY_TRACK/` or `Workspace/WIN32/LEVELS/MY_TRACK/`. Build and export, then inspect the resulting folder. Build includes these files in the dependency PACLOG even when XML does not reference them. Avoid placing different versions of the same DATA resource in both folders: the compiled WIN32 copy takes precedence. Copied stock levels also have a private `.runtime` snapshot in their source folder; normal workspace WIN32 files override that snapshot during publication.

## Lua lifecycle

Use **Track → Track files and Lua…** to open the owning content folder or edit `level.lua` in Notepad. The editor creates a starter only when the track has no script. Existing scripts, including scripts retained when copying a stock level, are preserved. An installed or snapshot script is copied into the workspace before editing. Save the script and use **Ship → Build level** before exporting or testing. The dialog reports when the installed game needs the level-content update; authoring and exporting remain available.

The game looks for `DATA:/LEVELS/MY_TRACK/level.lua` beside the loaded level. It executes the file in the `Level` environment. Define these optional callbacks there:

```lua
local state = { started = false }

function Start()
    state.started = true
    -- Set up the track's scripted behaviour after the level becomes ready.
end

function Stop()
    state.started = false
    -- Release script-owned behaviour when the game begins leaving this level.
end
```

The engine calls `Level.Start()` when ready and `Level.Stop()` before unloading, outside editor mode. It removes the `Level` and `ActorInstances` tables on unload. Use the game's existing Lua APIs for actor interaction and additional script loading; placing a module or data file in the folder does not execute or consume it automatically. Script errors use the game's normal Lua diagnostics.

Scripts use the existing game Lua environment. Track-folder ownership rules do not create a separate Lua sandbox, and scripts should clean up changes they make outside their `Level` table.

Declare mutable state at file scope, as in the example, or inside a table created there. The existing namespace loader prevents creating new top-level namespace variables after the file has loaded.

## Copies, updates and records

**Save copy as new level** preserves owned runtime files byte for byte. It renames level-named PWS and NAVMESH files to match the new level, while preserving `level.lua` and other file names. It does not rewrite hardcoded old level paths inside scripts or data; update those when authoring the copy.

Lua, configuration, text/data files, navigation, spline, streaming and vegetation changes contribute to the layout hash, alongside existing geometry. Their updates therefore change the identity used for records and exact-layout multiplayer matching. Audio and terrain colour maps are treated as cosmetic content. Installed test tracks continue to use `dev: true` and do not post leaderboard scores.

Event ids are derived from the package id and a persistent actor/registration key. Event gameplay settings contribute to separate layout hashes, so different routes and stunts do not share times or scores. Renaming a display label or changing minimap artwork preserves that event's records; changing its actor selector, mode defaults, weather or bomb tuning changes its hash. Existing legacy manifests keep their original hashing rules. Upgrading an export to event format 1 establishes the new per-event identities.

Local mod records include stunt, survivor, keep-the-flag and deathmatch scores as well as lap times and carnage scores. They remain in the existing `ModTrackRecords.json` sidecar; the player-save layout is unchanged. Release tracks can use the same leaderboard categories as stock events: lap, time trial, carnage, bomb and stunt. Arena and speed-trap modes have no Steam board category. Development exports do not register or post public scores.
