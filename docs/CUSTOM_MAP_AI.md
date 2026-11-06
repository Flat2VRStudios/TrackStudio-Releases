# Custom map AI and object collision

These controls are opt-in source data owned by a race or object. Unmarked maps retain the game's existing behavior. The runtime integration is under validation; a built draft is not a verified driving course.

Open the **Track** panel and choose **Custom map AI → Settings**. **Game defaults** removes the race-path opt-in. **Custom map profile 1** offers two independent settings:

- **Keep wall avoidance inside the driving corridor** bounds the original driver's wall response and release using its current and future lane limits.
- **Use the corrected steering response** makes the original driver approach its requested steering endpoint at the existing steering rate.

The improved driver's existing behavior is retained. External driving models such as the FO2 driver use their own steering and planning. No global AI tuning command is needed. The old diagnostic `bound_original_wall_avoidance` switch is rejected by the updated game.

The settings live on the race's `Rc2RacePath` actor as `Rc2CustomMapAI`. A shared path shares its profile. Each race resolves its own policy; reset and stop clear it. Missing, unsupported or invalid profiles use the legacy policy. Profile 1 with bounded avoidance requires at least two control points in each section and an explicit finite marker width on every node. Zero is valid; `-1` is unknown for this profile. Track checks report misplaced profiles and missing widths, and release exports block these errors.

## Lane widths and validation

Use the AI section's **Markers** action to set symmetric or independent left/right widths and **Blend widths between markers**. Widths measure from the line to the authored edge. The native AI reserves its car half-width once. The existing AI corridor overlay displays the authored corridor.

For an enabled custom profile, **Fit AI room to road** uses the editor's road geometry with a 0.25 m edge margin, permits zero room and interpolates widths. It refuses unsupported nodes instead of inventing a wide corridor off every road. Inherited races retain the previous fitting defaults. Imported static collision needs the native check below.

**Check in game** reads the paused, loaded race through its native bridge. Supply the exact process ID and vehicle index. It refuses a different level, different section layout or different control-point positions. It reads native road-edge stop reasons, limits proposed widths to existing source intent, and checks static centre and usable edge poses with the selected car's actual wheel/chassis routines. It never drives, teleports or resumes the game.

A clear report enables **Apply measured widths**, a separate undoable edit. It changes marker limits/interpolation and keeps the route. Reports are saved under the workspace's `.trackstudio/ai-checks` folder with a source fingerprint. Editing source invalidates them; reloading the same source can recover the report.

The panel distinguishes static clearance from acceptance. Matching path coordinates alone does not authenticate the loaded collision assets. Static poses do not prove continuous motion between nodes, moving-prop clearance, every car model, checkpoint order or a counted lap. Keep the existing Track gate/junction checks and run genuine counted AI laps. Native opponent finish estimates after the player's finish are not evidence of a completed AI lap.

## Object collision

Select an object with a **MeshCollider** and open **Custom object collision → Collision settings** in the Track panel. These settings also appear in the component inspector.

- **Inherit existing behavior** preserves its original PSI flags and body/group settings, including previously imported roles.
- **Custom object profile 1** explicitly chooses racing-chassis contacts and wheel support.
- **AI collision classification** can inherit its group, use **Ground (Terrain)** or **Wall / barrier (Default)**. The native wall scan sees Default and excludes Terrain. Floors and barriers needing different roles must be separate objects.

This is a per-collider override applied when its native shapes are created. Loose-prop and wreck contacts keep the existing imported-role semantics. Unsupported profile/role values inherit the original shape behavior and produce an editor error. A rebuild and reload applies changes. Existing breakable bodies, joints, fragments and destruction rewards remain authored through their normal object components; collision-role settings do not generate them.

## Export and game support

Source edits use ordinary editor history, save/reload and level compilation. Exports retain the profile and declare `customAiFormat: 1` or `customCollisionFormat: 1` in `track.json` as needed. Installation checks the game's `FO_TRACK_CUSTOM_AI_1` and `FO_TRACK_CUSTOM_COLLISION_1` capabilities before replacing an installed track. Unsupported games can still receive an exported folder for later use. Unsupported manifest versions are rejected by Track Studio.

The engine changes are supplied in `tools/game-patches/custom-map-policies.patch` after the earlier AI diagnostics, target-room and imported collision-role patches. Build matching game and engine binaries together. Updating Track Studio alone does not update an installed game.

The MCP tools are `set_custom_map_ai`, `set_custom_object_collision`, `inspect_custom_map_ai` and `apply_custom_ai_rooms`. `set_ai_markers` also accepts `interpolate`. They use the same Core edit and inspection operations as the UI.

## Validation status

The native fixtures extract the production methods and pass 40,841 AI/default/fallback checks and 1,994 collision checks. Existing steering, wall-target and MeshCollider fixtures also pass. The packaged patch was applied to saved prerequisite sources and reproduced all 16 modified files byte for byte; reverse application was checked against the native build sources.

A same-process native test passed custom race → stock Forest → custom race → restart, with zero queued native errors at all four checks. Both custom AI flags were off on the stock track and enabled again on the custom race. The test used a private diagnostic profile and matching rebuilt game/engine binaries.

The editor/port builds and 133 focused Core/MCP/export tests pass. The full Core run passed 2,408 tests and skipped one. Its release-archive fixture failure was corrected and retested; four remaining failures depend on locally modified game assets (Forest vegetation counts and malformed tutorial GIS files), with the Forest count failures reproduced on main. Those source assets and expected canonical counts were retained.

The full Timberlands editor self-test passed all 459 checks with viewport captures and no level-file changes. Before/after benchmarks on Timberlands and Forest retained the same actors, models, draw calls and triangle counts across all tested views. Their timing spread is not a controlled performance claim.

The Lakeside trial remains an unverified conversion draft. Five of six cars recorded genuine timed laps; the sixth missed checkpoints after recovery. Static clearance does not resolve that checkpoint/recovery failure. No prepared Burnout variant is promoted to runtime-verified by these controls, and no installed shipping game is updated by the source patch.
