# FBX material import

**Create → Import 3D model (FBX)… → Use the model's own textures and colours** reads the texture connections exported in the FBX material. It supports `DiffuseColor` (albedo), `NormalMap`, `EmissiveColor` and `EmissiveFactor` (emission strength). Blender's FBX exporter writes these channels for supported Principled BSDF image and Normal Map node connections. Procedural or more complex node networks need to be baked to images before export; Track Studio cannot recover a connection that the FBX does not contain.

Images can be embedded, relative to the FBX, at their saved file path, beside the FBX, or in its `textures` folder. The desktop editor decodes PNG, JPG, TGA and BMP; the MCP importer handles PNG and 32-bit TGA. Missing or unsupported connected maps produce material warnings in the import result and Output. An absent optional map does not produce a missing-map warning.

The importer keeps the model, material sources and compiled resources under the track's `GRAPH/MODS/<LEVEL>/` namespace. Reimport an existing FBX to create a new prop with these maps; existing materials are not rewritten automatically.

The editor's source material bindings keep the original FBX node names and material slots; the game's compiled bindings use the combined imported prop. Solid imports keep the visible surface as well as its collider. If an older imported prop appears as a solid colour only in the editor, reimport it under a new name with this version to create corrected source bindings.

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
