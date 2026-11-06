# Track Studio 0.2.9

- **Import 3D model**, the Asset Editor's **...** buttons and the other file browsers that don't open in your track's folder start in the folder you used last, or Documents the first time. 0.2.8 opened every one of them in Track Studio's install folder.
- The Asset Editor's **...** buttons open the file browser over the Asset Editor, which waits for it, titled with the field and asset it is for. It used to open over the main window and leave the Asset Editor clickable. A file picked after the Asset Editor closed, or after its asset was edited, reloaded or shown for another platform, is no longer written to it, and if Windows can't show the browser the Asset Editor says so instead of Track Studio closing with an error.
- A **Browse**, **...** or **Save** button pressed in any window while a file browser is already open brings that browser to the front. It used to do nothing.
- In a developer checkout, the game-rendered **Rebuild probes** names the repair of older imported models in its first question and writes nothing if you cancel.
- **Rebuild probes** stops if another level is opened while it asks a question or builds, instead of updating, repairing or rebuilding the level that is open then.
- After a probe rebuild the viewport always returns to its own size, even if reloading the lighting fails.

## Previous release: 0.2.8

- Restores the modern Windows **Open file** dialog, with its address bar, search and navigation sidebar. It opens directly on its own desktop thread in the project's Levels folder, keeping the responsiveness and repeated-click crash fixes from 0.2.6.
- Keeps cancellation, reopening, file-type filters and Unicode file paths working through the modern dialog. The Output log records its initialization time.

## Previous release: 0.2.7

- Probe maps rebuilt by **Ship → Lighting probes → Rebuild probes** face the right way. In the game, three faces of every compiled reflection and lighting cube were on the wrong side (the sky lit things from the side and the horizon showed in upward reflections); in the editor the rebuilt reflections were mirrored left to right. Rebuild the probes and build/export the track again to update them. Checked against the engine's own compiled probes.
- Opening another level while probes are rebuilding no longer leaves the viewport stuck in game lighting at a small capture size, and probe maps are written through a checked temporary file so a full disk can't leave a truncated one.
- **Rebuild probes** works on workspaces in OneDrive folders, or in a Documents folder moved to another drive. The imported-material repair refused them as "file or directory links"; it now refuses only real links inside the workspace.
- The imported-material repair also handles models imported with no level open and names that aren't all upper case. It used to skip the first silently and refuse the second, blocking the probe rebuild.
- Changing an AI section's widths with **Markers…** or `set_ai_markers` keeps each marker's own **Blend widths between markers** setting. 0.2.5 rewrote it on every marker of the section, which changed how the AI drives sections that mix the setting, such as stock tracks'. Switching it in the form still sets it on all of them, markers that **Every node** adds take the section's setting, and when the markers differ the form offers keep, blend on all, or blend on none.
- After **Test in game**, a save file that appeared during the test (for example one Steam Cloud brought down meanwhile) is left in place instead of being moved to the workspace's `.trackstudio/profile-backups` folder. The files you had before the test are still put back.
- A custom AI or collision profile set back to 0 in the component inspector no longer makes **Install** and **Test in game** refuse the track as needing a game update; only enabled profiles need it.
- Custom map AI checks and **Check in game** consider only the AI sections the game builds. A stale section that no route reaches no longer blocks release or makes every in-game check fail with "section count differs".
- Conversion drafts no longer turn frozen source collision (a frozen body, or one under a frozen actor) into live, invisible collision.
- A model placed both by actors that use its mesh physics and by actors with primitive colliders converts both ways; one of the two sets of placements used to fail with "Conflicting converted asset".
- A mesh actor whose rigid body has its primitive colliders on child actors no longer also gets its model's mesh physics as a second collision.
- The conversion draft's result lists the primitive colliders it placed and the source collision it left out, with the reason.
- If Track Studio or Windows stops while the prop pack is being installed, the next start puts your previous content pack back instead of finding no pack at all, and old copies left beside the pack are removed. A copy whose cleanup was cut short is never put back.
- When the game closes before a test race starts, the message names the game's own `FlatOut 4.log` in its install folder (not a developer-checkout path) and says when Steam isn't running.

## Previous release: 0.2.6

- Prevents repeated **Open file** clicks from crashing while the Windows file browser is starting. The button immediately shows **Opening...**, stays disabled until the request finishes, and recovers after cancellation or failure.
- Opens the file browser directly on a separate desktop thread, starting in the project's Levels folder. This avoids the storage-picker/library startup delay, keeps the editor responsive, and records browser initialization time in the log. Folder selection uses the bundled desktop picker API.
- Fixes false refusals when repairing existing FBX imports with large tiled UVs. The repair verifies the game's actual stored UV representation while retaining its geometry, triangle-order and material-slot checks.
- Explains an unverified material match with the failing slot/triangle and recovery actions, instead of asserting that the user changed the FBX. Original material and placement edits remain preserved.

## Previous release: 0.2.5

- Repairs known older imported FBX material bindings in place before **Ship → Lighting probes → Rebuild probes**, with source backups and preserved material edits, geometry and actor placement. Reimporting is no longer required for this known binding problem.
- Rebuilds probe visibility for each cube face, waits for repaired textures, corrects cube orientation and excludes editor highlights and display modes from the capture.
- Corrects rebuilt cube-map DDS headers and reflection uploads so the new lighting and reflections reload into the editor after baking.

- **Test in game** starts the installed game for players who have never played Wreck Run; it no longer refuses because `WreckRunA.dat` and `WreckRunB.dat` are missing. Your saves are still put back as they were after the test, and a Wreck Run file the test created is moved to the workspace's `.trackstudio/profile-backups` folder.
- **Test in game** protects the saves of the Steam account you're signed in as. It used to pick the account with the newest save, so on a PC with more than one Steam account a test could change your save without putting it back.
- If Track Studio or Windows stops while the prop pack is being installed, the next start puts your previous content pack back instead of finding no pack at all, and old copies left beside the pack are removed.
- When the game closes before a test race starts, the message names the game's own `FlatOut 4.log` in its install folder (not a developer-checkout path) and says when Steam isn't running.
- Adds opt-in **Custom map AI** and **Custom object collision** controls to the Track panel, with matching MCP tools, undo and game-capability checks. Native clearance reports propose narrower lane widths without changing the route. Tracks without these settings keep the game's own behaviour; tracks that use them need a matching game update (`tools/game-patches/custom-map-policies.patch`) and counted driving tests. See `docs/CUSTOM_MAP_AI.md`.
- Conversion drafts keep static box, sphere and capsule colliders with their sizes, masks and supported rigid-body settings. Parent-owned compounds keep their intermediate actors and local transforms. An actor with a primitive collider keeps its visual model separate from any optional mesh physics.
- Explains why an older FBX import can show plain colours in rebuilt reflections, and how to repair it ("Solid colours in reflections" in `docs/FBX-IMPORT.md`).

## Previous release: 0.2.4

- Adds **Save copy as new level** for installed FlatOut tracks, preserving editable actors and owned runtime data in a separate workspace.
- Preserves a track's race, arena and stunt events, including modes, layers, weather/time presets, minimaps and bomb settings, in exported track folders. Expanded event and script features require a matching game build.
- Adds **File → Create FlatOut conversion project…** with a separate destination, optional source route, explicit collision mappings and a saved report of remaining work. These projects remain conversion drafts; development exports are available and release exports stay blocked.
- Adds **Track → Track files and Lua…** to open the track's content folder or edit its level script. Existing scripts are preserved; a new script receives a start/stop template.
- Retains explicitly mapped collision groups through static and animated imported assets, placement and export, with separate instance identities for different policies.
- Keeps basic single-race installation compatible with the original track-folder runtime, while refusing unsupported event settings or resources before replacing an installed track. Complete exports retain every event and setting.
- Restores textures when **Show/hide → Scene → Textures** is enabled after models have loaded, including cached props and generated road geometry.
- Preserves camera rotation and movement speeds when restarting or changing projects, and saves fly-speed changes from the speed controls and mouse wheel.
- Makes deliberate selection changes undoable, so an accidental selection can be undone before undoing a move. Selection history does not mark level files as modified.
- Places conversion-draft models in one scene refresh and undo step, with progress and cancellation, reducing repeated work on large imports.
- Imports FBX normal and emission maps, with OpenGL/Blender or DirectX normal conventions, correct tangent space and clear warnings for stock-shader emission limits or missing maps.
- Fixes imported FBX props appearing as plain colours, or disappearing when solid collision is enabled. Reimport an older affected prop under a new name; existing assets are preserved.

Saving and recovery:
- Keeps a crashed session's recovery copy until you choose **Restore** or **Discard them**. **Later** asks again next time; autosaves no longer delete it while the question is open.
- Saves every change you made: an edit made after undoing past a save, an edit older than the undo limit, and a terrain stroke made after undoing past a save are no longer treated as already saved.
- Drops a prefab's unsaved edits when you close the level without saving, instead of carrying them into the next level.
- Saves a new prefab even when the layer that uses it is unloaded.
- **Save layers of the selection** writes all the chosen layers or none of them.
- **Save mine anyway** saves only the files that save was for.
- Never saves over a terrain tile it couldn't read; the save names the tile to undo instead.
- Leaves no partial temporary file beside a level when a save fails, for example on a full disk.
- Takes an emergency recovery copy before writing a crash report, and keeps running when a crash report can't be written.

Editing:
- Viewport shortcuts no longer fire while you type in the paint and road panels: Delete, letters, Space, Enter and Esc go to the text box.
- Editing one value of a range on several actors with different ranges changes only that value.
- Duplicating or pasting a prefab instance keeps the children it removed from the prefab removed.
- Duplicating a parent together with its selected child copies the child once.
- Reordering actors in the Outliner keeps them exactly as they were written.
- Saving keeps comments and each line's own line ending; undo puts attributes back in the serializer's order.
- Cut no longer deletes the actors when the clipboard is busy; a notice asks you to try again.
- **Check level** and **Sink selected** work on a copy of the level, so editing while they run is safe.
- Opening a level while another is still loading is refused with a notice instead of leaking the first one.

Viewport:
- Uses the game's own shadow cascade fit and depth bias: contact shadows stay attached to poles, walls and cars, and cascade edges no longer crawl as the camera moves.
- Projected decals no longer corrupt transparent parts drawn after them.
- Deleted lights, probes, effects, decals and colliders stop drawing and picking at once, with their children.
- A deleted road, spline scenery, lake or decal (or an undone one) stops drawing at once instead of staying until the next road edit.

New tracks and terrain:
- New terrain tracks get vegetation and streaming files in the form the game reads (one instance count per 64-sample chunk).
- Imported multi-tile worlds place brushes, vegetation and chunks on the right tiles.

Project setup and updates:
- A damaged or newer content pack manifest returns to project setup with guidance instead of a crash.
- After an update, uses the content pack that came with the new release, and tells you which pack it replaced.
- Warns when your tracks folder is inside the Track Studio folder (an update would delete it), and no longer suggests one there.
- Clears what an interrupted prop-pack install left behind before checking free space.

Sharing:
- Basic single-race installation preserves settings on the original track-folder runtime; expanded events or unsupported settings still require the updated game.
- Keeps a slow Workshop upload going while Steam reports progress.
- Names the missing `steam_api64.dll` instead of showing a loader error, and logs the Workshop helper's errors.
- Checks the name and description against Steam's length limits before creating the Workshop item.

Import and tools:
- Re-importing a map from another game asks before replacing your edits, and backs up the old files when you replace them.
- A damaged texture in an imported map no longer stops the import; packaged files with unsafe names are refused.
- `TrackStudio.exe --help` lists the options; an unknown option or a missing value ends with exit code 2 instead of opening the editor.
- MCP: a cancelled request no longer breaks the link to the editor, tools that can delete or overwrite say so, and `list_assets` stays on the project's drives.
- Test runs that stop answering end with a clear message instead of waiting for ever.
- `THIRD-PARTY-NOTICES.md` lists every bundled component with its licence text.
- **Help → Getting started**, **Help → Publishing to the Steam Workshop**, **About Track Studio** and the Prefabs tab's **Open Releases** link open the public Track Studio page instead of a private one; bug reports go to its Issues.

The starter content pack is included. The optional **0.2.2 full prop pack** remains compatible. Complete imported-track conversion and certification of every race/stunt remain separate work; creating a conversion draft does not certify a playable port.

## Previous release: 0.2.3

- Fixes custom FBX imports with solid collision by bundling `TrackStudio.PhysXCooker.exe`. Players no longer need to find or build the helper themselves.
- Rebuilds generated WinUI resources during release packaging to prevent stale XAML from breaking startup.
- Prevents duplicate meshes and helper markers when parent and child edits reach the viewport in the same frame, and discards queued nodes from replaced actor trees.
- Adds **File → Check FlatOut conversion...** for imported maps, with background progress, cancellation, actor lookup and saved reports. It checks captured scene state and scenery along splines while editing continues.
- Expands imported asset support for materials, textures, collision, terrain, spline scenery and animation, with corresponding preview and conversion checks. Imported maps remain for inspection and editing; complete track conversion, in-game racing and publishing remain restricted.
- Improves imported material rendering, texture uploads and shader handling, including PBR and atlas previews.
- Preserves pending save choices and unsaved scene state when closing or switching maps, and waits for conversion-check cleanup when the editor closes.
- Includes the complete video tutorial and illustrated interactive guide links in the player guide.

The starter content pack is included. The optional **0.2.2 full prop pack** remains compatible; existing installations do not need to download it again. PhysX libraries are read from your own installed game and are not bundled.

## Previous release: 0.2.2

- Includes the collision helper required for custom FBX imports with solid collision, and rejects incomplete release archives that omit it.

- Opens FlatOut 4 VR's compiled stock tracks directly from the installed game, without a source checkout.
- Adds **File → Import map from another game...** for compatible engine games, including WRC 9, TT Isle of Man 2 and V-Rally 4. Imports are for viewing and editing; building, testing and publishing them into FlatOut 4 VR are disabled. The original game files are only read.
- Includes the road fitting tools, native road writer and accumulated rendering, terrain and import improvements since 0.2.1.
- Reopens your game, content pack and workspace when you restart Track Studio.
- Returns to project setup with recovery guidance when a project folder is unavailable.
- Keeps a fresh distributed copy on project setup instead of opening an unrelated nearby source checkout.
- Fixes the beginner checklist opening on retail projects and adds direct buttons for its next steps.
- Keeps the build step pending while the level is unbuilt or has unsaved changes.
- Names the viewport tools, save/undo/redo actions and folder browse buttons for assistive tools.
- Explains an empty level search instead of leaving the list blank.
- Fixes actor rows missing from the published app's Outliner.
- Bundles a small scenery catalogue so your first track can use barrels, tires, crates, barriers and signs without the optional full prop-pack download.
- Preserves the selected actor when Outliner rows refresh or a search has no matches, keeping duplicate and vegetation-type actions reliable.
- Frames the race on first open, opens the beginner guide for a new race, and shows its AI line, checkpoints and grid.
- Keeps the editor usable when restoring a window on a high-DPI display.
- Adds easy-to-grab panel dividers, keyboard resizing, View menu visibility controls and a reset layout command. Panel sizes and visibility are remembered per workspace.
- Lets automated game tests resolve an installed track folder without shared race registration.
- Uses the game's track key when starting a folder track's race automatically.
- Restores the previous installed test track if its replacement cannot be published.
- Lets you type or browse to the export destination in the track details form.
- Keeps direct-launch save protection active until the test game exits.
- Clarifies that time presets also preview their authored weather.
- Starts new race and arena tracks at the starter's dry noon setting, matching their default game preset.
- Gives new terrain a grass colour map instead of the engine's bright-green missing-map fallback.
- Tracks Windows file pickers so automation can cancel the editor's own selection operation safely.
- Verifies downloaded prop-pack files before replacing the current pack; failed or cancelled installations preserve it.
- Refreshes viewport assets when switching workspaces that use the same game installation.
- Stops a cancelled Workshop publication before starting Steam or submitting its upload.
- Retries short-lived Windows folder locks while publishing an export or installing a prop pack.
- Checks large installed content packs in the background so project setup stays responsive.
- Keeps your selected game, content pack and workspace in the Project form and when recovering from invalid startup folders.
- Separates same-named tracks' locks and recovery copies across workspaces, and prevents a recovery copy from changing another workspace.
- Keeps the current dialog and unsaved work intact when Close is pressed repeatedly.
- Runs Workshop operations in a separate helper so Steam doesn't mistake the editor for the running game and block Test in game.
- Restores or frames the camera after viewport initialization when reopening a track at startup.

The optional full prop pack is a separate download. New tracks can be created with the bundled starter content pack. See README.md for setup, editing, testing and sharing.
