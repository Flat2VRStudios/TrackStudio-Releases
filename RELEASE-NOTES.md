# Track Studio 0.2.3

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
