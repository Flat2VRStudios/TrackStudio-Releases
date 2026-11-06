# FBX material import

**Create → Import 3D model (FBX)… → Use the model's own textures and colours** reads the texture connections exported in the FBX material. It supports `DiffuseColor` (albedo), `NormalMap`, `EmissiveColor` and `EmissiveFactor` (emission strength). Blender's FBX exporter writes these channels for supported Principled BSDF image and Normal Map node connections. Procedural or more complex node networks need to be baked to images before export; Track Studio cannot recover a connection that the FBX does not contain.

Images can be embedded, relative to the FBX, at their saved file path, beside the FBX, or in its `textures` folder. The desktop editor decodes PNG, JPG, TGA and BMP; the MCP importer handles PNG and 32-bit TGA. Missing or unsupported connected maps produce material warnings in the import result and Output. An absent optional map does not produce a missing-map warning.

The importer keeps the model, material sources and compiled resources under the track's `GRAPH/MODS/<LEVEL>/` namespace. Reimport an existing FBX to create a new prop with these maps; existing materials are not rewritten automatically.

The editor's source material bindings keep the original FBX node names and material slots; the game's compiled bindings use the combined imported prop. Solid imports keep the visible surface as well as its collider. Older imports can be repaired in place when rebuilding probes, without reimporting the FBX or replacing the placed actors.

## UV tiling and texture settings

A road can look correct in the editor but stretched in game if its mesh has large tiled UV coordinates. The editor previews the source FBX's floating-point UVs; the game's rigid P3D mesh stores each UV component as a signed 16-bit value, with a supported range of **−8 to +8**. Earlier Track Studio imports silently clipped larger values: for example, 24 became 8. Vertices that should have different coordinates can then share the same one, stretching a texture across a triangle. This is separate from material bindings and probe maps.

New imports check the UVs used by the visible LOD0 meshes before creating materials or model files. The error names the mesh and coordinate, explains the range and suggests a correction. The check uses the same UV-layer selection and **V → 1 − V** conversion as the importer; authored FBX V values must therefore be between −7 and +9. Missing UV layers keep their existing fallback behaviour.

For a stock Generic material, keep the mesh UVs inside that range and use its **TileUV** material parameter for repeated textures. Its four values are `U scale;V scale;U offset;V offset`, defaulting to `1;1;0;0`. It transforms the mesh UVs before sampling the diffuse, normal and structure maps. A useful authoring correction is:

1. In the modelling app, scale the UVs down by a positive factor around UV **(0, 1)**, so `u' = u / factor` and `v' = 1 + (v − 1) / factor`. This pivot accounts for the importer's V flip. Choose a factor that brings every used coordinate into range.
2. Multiply the material's existing TileUV U and V scales by that factor; keep its offsets. For example, with default tiling, a factor of 16 gives `16;16;0;0`. Apply this only to materials whose mesh UVs were scaled; shared materials and custom effects need separate consideration.
3. Re-export the FBX, import it under a new name, set the new material's TileUV through **Track → Material parameters…** (or the asset's material fields), replace the affected Mesh resource and check the result in game. Normal and emission maps should be checked too.

An existing import's clipped compiled UVs cannot be reconstructed from the P3D alone. Correct its original/staged FBX and rebuild or reimport the model. Binding repair preserves old geometry; it does not recover this lost tiling. Rebuilding probes or editing a texture's filtering does not repair clipped mesh coordinates.

**MipMapFiltering is not runtime texture wrapping.** In a `.PTX.XML` it controls edge handling while the content compiler generates mip levels. The stock Generic `.PFX.XML` already uses **Wrap** addressing for diffuse, normal and structure maps. See the [texture and material XML reference](TEXTURE-MATERIAL-XML.md) for the separate settings and their exact names.

### Solid colours in reflections

On a retail install, **Ship → Lighting probes → Rebuild probes** captures Track Studio's own rendered scene. An older FBX import with broken editor material bindings can therefore look textured in the game while its rebuilt reflections contain the editor's plain fallback colours. The compiled model and the probe maps are separate resources: a working game material does not correct a reflection already baked from the faulty preview.

Use **Ship → Lighting probes → Rebuild probes** in a build containing the existing-import repair. It checks the imported FBX resources used by the open level, repairs the known older source-binding layout, reloads the models and textures, and then renders the probes. Material assignments and edits, the staged FBX, compiled geometry, textures, collision data and actor placement are preserved. Original source descriptors and a repair receipt are backed up below the workspace's `.trackstudio/repairs/fbx-bindings/` folder. Repeating the command does not repair or back up already-corrected imports again. The same check and repair run whenever probes are rebuilt: the Probe component's **Rebuild** buttons, the automatic probe build after a level build, and, in a source checkout, the game-rendered rebuild, which checks first, names the repair in its "Build first?" dialog and writes it only if you go ahead.

The repair verifies the original mesh/slot order against the staged FBX and compiled model. A changed mesh, missing material or custom collision layout is reported for review before any repair or probe map is written; it does not guess assignments or partially repair a batch. Stock assets and unrelated resources are left alone. The check covers imports under `GRAPH/MODS/` and `GRAPH/TRACKS/IMPORTED/`, where the editor puts them and where the MCP's `import_model` asks for a track's props; an older import saved anywhere else is not checked, so reimport it under `GRAPH/MODS/<LEVEL>/`. Version 0.2.4 fixed new imports, but did not contain this in-place repair.

Older compiled imports can store tiled texture coordinates differently from the FBX. Repair accounts for that original encoding rather than treating the difference as evidence of a re-export. If verification still fails, the message identifies the material slot and triangle it could not match; this does not by itself prove that the FBX was changed. Use the FBX from the original import, or import the current model under a new name and update the affected actors' Mesh resource.

Capture waits for the repaired textures, builds visibility separately for every cube face, stores the DDS mirrored in X as the game's own probe captures are (so the editor and the engine's texture compiler read it the same way), writes the compiled PTX faces in the order the game's loader expects, and omits editor helpers and selection highlights. It captures textured, lit surfaces even when the viewport's meshes, textures or game lighting are hidden, then restores the view settings. The rebuilt DDS cubes keep all six faces and reflection mip levels and reload into the editor's lighting. The filter remains Track Studio's CPU approximation, not CubeMapGen-identical. Build/export and install the updated track afterwards so the new probe maps reach the game.

## Normal maps

Normal maps use linear data, not sRGB. The FBX's `BumpFactor` normal strength is baked into the tangent-space map. The source texture is marked `Format NORMALS`, while the directly written uncompressed runtime texture stores X in alpha and Y in green, matching the game's PC normal sampler. An emission material without an authored normal map receives a flat normal map because the stock effect's emissive trigger combination requires normal mapping.

Choose **OpenGL / Blender (+Y)** for ordinary Blender normal maps, or **DirectX (-Y)** for maps already authored that way. FBX import flips V for the game's texture coordinates; the OpenGL choice converts the green channel once. Imported rigid meshes receive UV-aligned tangents and handedness, with separate vertices across mirrored UV seams, so the game can use the same frame as the editor. Missing UV layers are reported; degenerate UV triangles use a fallback tangent and cannot establish faithful bump direction.

## Emission

FlatOut's stock Generic effect reads emission intensity from the blue channel of `StructureMap`, clamps it to 0–1, and emits in the albedo colour. The importer creates this native mask from the exported emission colour and strength channels. Structure red/green are zero for the new imported profile (matte, non-metallic).

This does **not** reproduce arbitrary independent RGB emission or HDR intensity from Blender. The import result states that limit and retains the original emission images in the material's sibling map folder (`<material-name>/EMISSION.PTX.XML`, and `EMISSION_STRENGTH.PTX.XML` when present), so they can be reused in a compatible custom material. A custom shader requires the game's content compiler and matching runtime support.

The stock Generic effect has no combined alpha-test/self-illumination trigger variant. On a cutout material, emission maps are retained but emission is disabled with an explicit warning. Split the emissive surface into an opaque material or use a compatible custom material rather than relying on a missing shader variant.

The importer reports other exported texture channels, such as roughness or height/bump, that this profile does not map. A height map is not interpreted as a tangent-space normal map. Check imported materials in the editor and game before publishing.

## Developer verification

`ImportedMaterialMapsTests` exercises external and embedded FBX channels, native PMA/PTX readback, normal swizzling and strength, linear emission-strength packing, missing versus absent maps and unsupported stock shader combinations. Generate an original Blender fixture with `tools/import-tests/make_fbx_material_maps.py` in a private background Blender process and set `TRACKSTUDIO_FBX_MAP_FIXTURE` to the generated FBX to run the additional real-export check. The generator does not touch an open Blender session.

The tests also reopen imports through `MeshLibrary`, check their actual preview material paths, and compare transformed multi-node source triangles and bounds with the combined runtime geometry. New P3D source descriptors carry the editor-only `trackstudio-rigid-import="1"` attribute. It allows their explicitly retained surface to be shown while also used for collision; unmarked stock collision-only resources retain their existing visibility. The native descriptor loader ignores this metadata attribute.

Legacy repair compares oriented triangle positions and UVs in material-slot order. UVs are compared after the old writer's signed 16-bit fixed-point truncation and saturation (approximately −8 to +8), with exact encoded equality. This recognizes already-imported tiled source UVs outside the stored range while refusing a one-step change to the runtime UV stream; it does not widen the position tolerance, infer new material assignments or recover clipped UVs. New imports reject unrepresentable coordinates before writing their assets.
