# Moving individual objects in an imported track

Some imports put a whole level's tree leaves in one mesh, their trunks in another, and their inner leaf shells in another. Moving that mesh moves every included tree. Changing the handle position does not separate the geometry.

Select one of these merged meshes, then choose **Actor → Make imported objects editable…** (also in its right-click menu). The review shows how many original objects and meshes will be restored. Choose **Make objects editable** to continue.

When the import retains original object names, Track Studio matches those exact names across the material batches. For example, Moo Moo Meadows' four spherical-tree batches become **38 individual tree objects containing 114 material parts**. Each tree owns its bark, leaves and inner leaf shell. Clicking any of these parts selects that complete tree; move, rotate, scale, duplicate and delete act on that tree. Other trees stay in place. Its handles stay at the object's own center.

Expand a tree in the Outliner to see its parts. **Edit this part only** in a part's right-click menu gives access to its Mesh properties and materials. Clicking it normally selects the complete tree again. Selection changes are undoable too.

The restoration is one undo step. It retains original resources and batch UIDs, creates new source FBX/P3D/PSI files and matching game resources under the track's `GRAPH/MODS/<level>/EDITABLE_<id>/` folder, and reuses existing material/texture assets. Undo restores the old batches; generated resources remain for redo. Saving and reopening keeps the object structure. Retail builds contain ordinary parent/child actors and native model resources; editor selection markers are omitted from the game's merged level.

Restoration preserves current actor transforms, including edits already made to a batch. Undo an accidental level-wide leaf movement before restoring if you want its original placement back. Original files are never reimported or overwritten by the operation.

This operation requires retained `<original object>__<material>` mesh identities. It does not infer objects from proximity or separate a completely flattened unnamed mesh. It refuses ambiguous duplicate batches, locked/prefab/controlled actors, animation, additional LODs, unsupported collision, unrepresentable UVs and sheared transforms. An unsuccessful preflight leaves the actors unchanged and identifies what needs attention.
