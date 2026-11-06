# Texture and material XML settings

Use the Asset Editor or **Track → Material parameters…** for normal material editing. These notes explain the source XML when diagnosing an imported asset. Texture compilation settings, material values and effect sampler states are three different things. This is a reference for those settings, not a schema for every game XML file.

## Where a setting belongs

| File | Purpose | Example |
| --- | --- | --- |
| `.PTX.XML` | Texture source and content-compiler settings | Image path, compression, mip generation, colour processing |
| `.PMA.XML` | Values and feature triggers for the material's chosen effect | Diffuse/normal/structure image references, TileUV, Use NormalMap |
| `.PFX.XML` | Effect definition, supported trigger combinations and sampler states | Wrap/Mirror/Clamp addressing and filtering |
| `.P3D.XML` / FBX | Mesh source and geometry | UV coordinates before the material transform |

The game reads the compiled `.PTX`, `.PMA`, `.PFX` and `.P3D`. Editing a source descriptor does not by itself update a compiled file. Track Studio's supported material commands write the corresponding native material; changing texture compiler options or custom effects requires the matching content compiler. A retail install cannot compile a new effect. Do not edit a stock global effect to fix a single imported prop.

## Texture compiler fields (`.PTX.XML`)

Settings use text inside `param` elements under `play_all_settings/params`, with the appropriate platform. For example:

```xml
<play_all_settings>
  <params platform="GENERIC">
    <param name="ColorSpaceProcess">SRGB</param>
    <param name="Format">COMPRESS_NO_ALPHA</param>
    <param name="MipMapFiltering">WRAP</param>
  </params>
</play_all_settings>
```

The engine's texture content schema defines these field names (case matters):

| Field | Meaning / named values |
| --- | --- |
| `Format` | Compression/usage profile, including `COMPRESS_NO_ALPHA`, `COMPRESS_ALPHA` and `NORMALS`; available profiles depend on the platform's texture configuration. |
| `TextureType` | `2D TEXTURE` or `CUBE MAP`. |
| `GenerateMipMaps` | Whether the compiler generates progressively smaller image levels. |
| `MipMapsCount` | Generated mip-level count. |
| `AlphaToCoverage` | Preserve alpha coverage while generating mips, useful for cutout foliage. |
| `ATCAlphaRef` | Alpha reference used by that coverage processing. |
| `MipMapFiltering` | `WRAP` or `CLAMP` edge processing during mip generation. It does **not** select the game's runtime UV addressing. |
| `Resize_Value` | Compiler image-resize setting; preserve the format from an existing asset made by the matching compiler. |
| `ColorSpaceProcess` | `NONE`, `SWIZZLEGA`, `SRGB` or `SPEEDTREE_SPECULAR`. Colour images commonly use SRGB; normal/data maps require their own linear/profile handling. |
| `PureRGBA` | Compiler option for uncompressed RGBA output. |
| `CubeMapProcess` | `NONE` or `MIRROR_YZ` cube processing. |
| `NormalizeHDR` | HDR normalization processing. |
| `DitherMode` | `NONE`, `DITHER` or `DITHER_DIFFUSION`. |

There is no general PTX field named `Filtering` for switching a material between Wrap and Mirror. Also, Track Studio's direct FBX importer currently writes uncompressed single-mip runtime textures, capped at 1024 pixels; adding a compiler field to their source XML does not regenerate those runtime images.

## Stock Generic materials (`.PMA.XML`)

The selected effect defines the allowed parameters and trigger combinations. Generic exposes these parameters:

| Parameter | Type | Meaning |
| --- | --- | --- |
| `DiffuseMap` | `Texture2D` | Albedo image, with the `Use DiffuseMap` trigger. |
| `NormalMap` | `Texture2D` | Tangent-space normal image, with `Use NormalMap`. |
| `StructureMap` | `Texture2D` | Packed surface channels, including the emission mask in blue. |
| `DiffuseColor` | `Float4` | Surface colour when diffuse mapping is disabled. |
| `StructureColor` | `Float4` | Constant structure data with `Use StructureColor`. |
| `TileUV` | `Float4` | `U scale;V scale;U offset;V offset`, default `1;1;0;0`. |
| `SelfIllum` | `Float` | Emission control with `Use SelfIllum`. |
| `Occlusion` | `Float` | Occlusion value. |

PMA values use attributes, unlike the PTX compiler settings above:

```xml
<param name="TileUV" type="Float4" value="16;16;0;0"/>
```

This element belongs in the material's `params`. It does not enable a missing effect trigger. Generic only supports specific trigger combinations, including no combined cutout/self-illumination variant; see [FBX material import](FBX-IMPORT.md).

## Runtime samplers (`.PFX.XML`)

A sampler names a texture parameter and uses `addu`, `addv` and `addw` for addressing. The engine recognizes `Wrap`, `Mirror`, `Clamp`, `ClampToEdge` and `Border`. Its named filter-quality modes are `Custom`, `MediumQuality` and `HighQuality`; custom min/mag/mip filter modes include `Nearest`, `Linear` and `None`.

Stock Generic uses `Wrap` for all three axes of its diffuse, normal and structure samplers. Changing a PTX's `MipMapFiltering` cannot override those states. Likewise, even a Wrap sampler cannot recover mesh UVs that were already clipped during P3D export. See [UV tiling and texture settings](FBX-IMPORT.md#uv-tiling-and-texture-settings).

## Source references for developers

Paths below are relative to the Fox checkout:

- `PlayAll/Code/Tools/PlayAll/Tools/ContentPipeline/Content/Texture.cpp`: texture field names and enums.
- `PlayAll/Code/Tools/PlayAll/Tools/ContentPipeline/Content/EffectSampler.cpp`: sampler addressing and filter enums.
- `PlayAll/Sample/Data/Assets/Engine/Effects/Surfaces/PBR/Generic.PFX.XML` and `Generic.cg`: Generic parameters, samplers and UV transform.
- `PlayAll/Code/Tools/PlayAll/Tools/ContentPipeline/ProcessorWriter/SceneWriter.cpp`, `SceneProcessorSP2.cpp` and `Content/Vertex.h`: packed UV encoding and source range check (the latter is under the ContentPipeline directory).
