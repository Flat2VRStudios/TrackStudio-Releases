# Track Studio for FlatOut 4 VR

Build a track with the game's own art, race it in FlatOut 4 VR, and share it on the Steam Workshop.

This repository contains **public downloads and the player guide**. The editor's source code is maintained separately and is not published here.

## Get Track Studio

**[Download TrackStudio 0.2.3 for Windows x64](https://github.com/Flat2VRStudios/TrackStudio-Releases/releases/download/v0.2.3/TrackStudio-0.2.3-win-x64.zip)** · **[All release files and checksums](https://github.com/Flat2VRStudios/TrackStudio-Releases/releases/latest)**

1. Unzip the editor into a folder you can write to.
2. Run **TrackStudio.exe**. The **starter content pack is included**; no separate starter download or .NET installation is needed.
3. Check the detected game, content pack and workspace folders, then choose **Start**.

You need your own updated Steam installation of **FlatOut 4 VR**, Windows 10 version 1809 or later (64-bit), and a graphics card supporting Direct3D 11. You do not need a VR headset to build or test on your monitor. The editor download does not include the game or its content compiler.

### New in 0.2.3

- Custom FBX imports with solid collision now include the required `TrackStudio.PhysXCooker.exe`. Extract the complete editor download into a new folder; no C++ tools are needed.
- Release builds regenerate WinUI resources to avoid stale XAML breaking startup.
- Queued scene edits no longer duplicate meshes and helper markers when parent and child subtrees change together.
- Imported-map conversion checks run in a separate window with progress, cancellation, actor lookup and saved reports, alongside expanded asset conversion and rendering support. Imported maps remain for inspection and editing; complete track conversion, racing and publishing remain restricted.
- The recent editing, save/close, material preview and tutorial improvements are included.

Your existing workspace and **0.2.2 full prop pack** can be reused. See the [0.2.3 release notes](https://github.com/Flat2VRStudios/TrackStudio-Releases/releases/tag/v0.2.3).

## Learn Track Studio

[![Track Studio tutorial — build, edit, race and share a FlatOut 4 VR track](docs/images/trackstudio-tutorial.jpg)](https://gistpreview.github.io/?9c12eb4549742e2019ff128acefdf0b7)

**[Watch the full video tutorial (40 minutes)](https://youtu.be/LvMekSUe_pw)** · **[Follow the illustrated, interactive guide](https://gistpreview.github.io/?9c12eb4549742e2019ff128acefdf0b7)**

The 18 chapters cover project setup, navigation, scenery, roads, terrain, vegetation, race systems, custom assets, lighting, validation, building, a real in-game race, iteration, Steam Workshop publishing, learning from existing maps, MCP automation, and backups. The text guide includes screenshots, practice tasks, quizzes and progress saved in your browser.

## Optional full prop pack

The starter pack is enough for the first-track workflow. The optional **0.2.2 full prop pack remains compatible with 0.2.3**; it does not need to be downloaded again. For the larger scenery catalogue, download **all nine matching 0.2.2 parts** into one folder. The full pack is about **9.8 GB to download / 13.4 GB installed**. Keep extra space for the downloaded ZIPs until installation is complete.

[Part 1](https://github.com/Flat2VRStudios/TrackStudio-Releases/releases/download/v0.2.2/TrackStudio-PropPack-0.2.2-part1-of-9.zip) | [Part 2](https://github.com/Flat2VRStudios/TrackStudio-Releases/releases/download/v0.2.2/TrackStudio-PropPack-0.2.2-part2-of-9.zip) | [Part 3](https://github.com/Flat2VRStudios/TrackStudio-Releases/releases/download/v0.2.2/TrackStudio-PropPack-0.2.2-part3-of-9.zip) | [Part 4](https://github.com/Flat2VRStudios/TrackStudio-Releases/releases/download/v0.2.2/TrackStudio-PropPack-0.2.2-part4-of-9.zip) | [Part 5](https://github.com/Flat2VRStudios/TrackStudio-Releases/releases/download/v0.2.2/TrackStudio-PropPack-0.2.2-part5-of-9.zip) | [Part 6](https://github.com/Flat2VRStudios/TrackStudio-Releases/releases/download/v0.2.2/TrackStudio-PropPack-0.2.2-part6-of-9.zip) | [Part 7](https://github.com/Flat2VRStudios/TrackStudio-Releases/releases/download/v0.2.2/TrackStudio-PropPack-0.2.2-part7-of-9.zip) | [Part 8](https://github.com/Flat2VRStudios/TrackStudio-Releases/releases/download/v0.2.2/TrackStudio-PropPack-0.2.2-part8-of-9.zip) | [Part 9](https://github.com/Flat2VRStudios/TrackStudio-Releases/releases/download/v0.2.2/TrackStudio-PropPack-0.2.2-part9-of-9.zip)

In the editor, open **Prefabs → Install downloaded parts…** and select **any one** of the parts. The other eight are found beside it. Do not mix parts from different versions. If a level was open during installation, restart TrackStudio afterward.

## Your first track

1. Choose **New level**, give it a unique name such as `MY_TRACK`, and select the **Race track** template. Keep at least eight starting slots for a released track.
2. Hold **RMB + WASD** to fly; **Q / E** move down / up and the mouse wheel changes speed. Release RMB before using editing shortcuts. **Home** frames everything; **F** frames the selection.
3. Place props from **Prefabs**. **Q / W / E / R** select, move, rotate and scale. **Ctrl+D** duplicates, **End** drops to ground, and **Ctrl+Z** undoes.
4. Use **Draw road** to place control points. **Enter** finishes a road; **Esc** leaves the tool. Fit the terrain separately, and align the AI route, checkpoint gates, grid and recovery volumes with the road.
5. Save with **Ctrl+S**, run **Track → Validate**, read the findings, then choose **Ship → Build level** and **Ship → Test in game**. Test start positions, collision, opponents, checkpoint progression, recovery and the finish.
6. When the track works, use **Ship → Publish to Steam Workshop** or **Ship → Share as a file**. Preserve your editable workspace and Workshop association data for future updates.

The first road build can temporarily require free disk space about the size of the installed game. Saving editable sources and building game content are separate steps.

## Existing and imported maps

TrackStudio 0.2.2 can open FlatOut 4 VR's installed stock tracks without a game source checkout.

## Game compatibility

Track-folder racing and the Workshop upload/download round trip were validated on FlatOut 4 VR release-candidate build **26434887**. Track-folder racing on Steam public build **26352498** has not yet been verified. Use a game version supporting modded tracks and check the editor's readiness messages before sharing a level.

The full player guide and release notes are also included in the editor ZIP.
