Surface Format
==============

Surface (.sur) files are used for physics, i.e. collision detection. The
format is somewhat convoluted and not friendly to parsing. Notable
"features" are use of integers of unusual bit lengths and heavy use of
offsets to denote structures.

Header
------

| Type   | Name      | Description                                         |
|--------|-----------|-----------------------------------------------------|
| uint32 | Signature | FourCC signature. Must always be 0x73726576 (vers). |
| float  | Version   | Must always be 2.0                                  |

Following header are sequence of parts:

Parts
-----

| Type   | Name     | Description                                                 |
|--------|----------|-------------------------------------------------------------|
| uint32 | PartID   | FLCRC32 hash of object name in "Cmpnd/Part\_\*/Object name" |
| uint32 | Sections | Number of sections                                          |

partID is FLCRC32 hash of "Cmpnd/Part\_\*/Object name" or is 0 for .3db
models.

Every part minimally has extent and mesh sections.

The order of section does not seem to matter but all Freelancer files
follow the same order: non-fixed, extent, mesh, hardpoints. Therefore
section counter always is between 2 and 4.

### Not-Fixed Section

| Type   | Name | Description       |
|--------|------|-------------------|
| uint32 | Tag  | 0x64786621 (!fxd) |

When present indicates this part is moveable. Root part for .cmp or null
part for .3db should always have "not-fixed" section-flag. For other
parts it should be present if associated compound object joint isn't of
fixed type (Rev, Pris, etc). Section has no further data, just the tag.

### Extent Section

| Type   | Name      | Description               |
|--------|-----------|---------------------------|
| uint32 | Tag       | 0x73747865 (exts)         |
| float  | Minimum X | Minimum point coordinates |
| float  | Minimum Y |                        |
| float  | Minimum Z |                        |
| float  | Maximum X | Maximum point coordinates |
| float  | Maximum Y |                        |
| float  | Maximum Z |                        |

Bounding box for part, probably used for AABB comparison. Min/max
coordinates from mesh points.

### Mesh Section

Contains header, convex hulls, points buffer and BSP tree nodes, in
exact this order.

#### Header

| Type   | Name               | Description                 |
|--------|--------------------|-----------------------------|
| uint32 | Tag                | 0x66727573 (surf)           |
| uint32 | Size               | Mesh section length         |
| float  | Center X           | Sphere center offset        |
| float  | Center Y           |                          |
| float  | Center Z           |                          |
| float  | Inertia X          | Inertia vector (unused?)    |
| float  | Inertia Y          |                          |
| float  | Inertia Z          |                          |
| float  | Radius             | Sphere radius               |
| uint8  | Scale              | Sphere scale                |
| uint24 | Nodes end offset   | Offset to end of BSP tree   |
| uint32 | Nodes start offset | Offset to start of BSP tree |
| float  | Unknown X          | Unknown vector              |
| float  | Unknown Y          |                          |
| float  | Unknown Z          |                          |

-   Bounding sphere encompasses all hull points
-   Bounding sphere with radius \* scale encompasses only non-hardpoint
    hull points
-   Mesh section length doesn't include tag byte length.
-   Read scale as unsigned byte and divide by 0xFA.
-   Nodes start offset and end offset are relative to mesh section
    offset.

#### Hulls

Immediately after header hulls are listed. There's neither counter nor
length for them, so this block is is read until file pointer position
matches hull offset + offset to point block.

| Type   | Name                  | Description              |
|--------|-----------------------|--------------------------|
| uint32 | Offset to point block | Offset to points block   |
| uint32 | PartID/Node offset    | Object name hash         |
| uint8  | Type                  | Hull type                |
| uint24 | Reference count       | Number of refs in DWORDs |
| uint16 | Faces count           | Hull face count          |
| uint16 | Padding               | 0                        |

-   Offset to points block is relative to itself.
-   Type 4 is regular hull (but is hardpoint if partID is listed in hpid
    section).
-   Type 5 is wrap. Used for grouping multiple hulls, one per mesh. Not
    present if mesh contains only one hull.
-   If hull is wrap its partID instead will be offset to node in BSP
    tree.
-   Reference count is number of triangle point refs + hull header in
    DWORDs: (12 + triangle.count \* 6) / 4.
-   Convex hull will always have even number of triangles as simplex is
    made of four triangles, edge division splits adjacent faces in half
    while subdividing face replaces one for three.

Followed by this block repeated faces count times:

| Type   | Name   | Description |
|--------|--------|-------------|
| uint32 | Header | Face header |
| uint32 | Edge A | Face edge   |
| uint32 | Edge B |          |
| uint32 | Edge C |          |

Each face edge:

| Type   | Name   | Description                |
|--------|--------|----------------------------|
| uint16 | Index  | Index in points buffer     |
| int15  | Offset | DWORD offset to other edge |
| bit    | Flag   | True for wraps             |

-   Face header contains face index (12 bits), opposite face index (12
    bits), unknown (7 bits) and flag (1 bit).
-   Header flag bit is enabled on for wraps, disabled for face headers
    of other hulls.
-   Opposite index seem not used by game, according to Adoxa. *Obj2sur
    defaults it 1 for all faces except first*
-   As every edge is shared between only two faces the offsets point to
    one another.
-   Every face is 4 DWORDs, edge offset is by amount of DWORDs from
    current edge DWORD offset

Each hull is expected to be convex, otherwise unpredictable behavior may
occur during collisions, ranging from hits not being registered to
outright crashing the game. It would appear hulls in Freelancer files
are generated by quickhull algorithm. Some modeling software provides
tools to generate convex hulls from array of points using this
algorithm: MassFX/PhysX in 3Ds MAX, Convex Tool in Blender, etc. It is
recommended to keep number of vertices as few as possible and having
them homogeneously spread across mesh seem improve stability, a good
number to aim for is 30-40 vertices per convex mesh. Generally avoid
running convex tool over actual visible mesh, create much simplified
geometry from mesh and use it instead. Even convex meshes may fail to
work in Freelancer if they are too detailed or there are too many of
them.

Typically mesh section will contain hull IDs matching that of the part
containing mesh section, however additional hulls can be present for
hardpoints and sometimes for fixed compound children too, resulting in
duplicate hulls: a parent part having hulls for a child part (and those
hulls IDs matching child part ID) while child having its own hulls as
well. However this isn't always the case, not every hitbox in Freelancer
model seemingly created with this behavior, some do and others not. It
could be accounted for different exporter versions producing these
duplicates or perhaps something to do with destructible parts. An
example of duplicate parent-child hulls can be found in li\_elite.sur,
Root part containing hulls for wings as well, despite the fact wing
parts have hulls of their own.

A single special hull (type 5) will encase all regular hulls (type 4)
and hulls/shrinkwraps of all fixed children the associated compound
object has. *It would seem to be a case of a hull generated over all
points in point buffer of a mesh.*

However shrinkwrap hull will not be present if mesh has only one hull
and associated compound object doesn't have fixed children.

#### Points

Points are read until file pointer matches mesh section offset + nodes
start offset.

| Type   | Name       | Description       |
|--------|------------|-------------------|
| float  | Position X | Point coordinates |
| float  | Position Y |                |
| float  | Position Z |                |
| uint32 | PartID     | Object ID         |

-   Hull faces indices point to this block.
-   *Having PartID at every point seem to be redundant given each hull
    has PartID anyway.*

#### BSP tree

Tree nodes are read until file pointer match mesh section offset + nodes
end offset.

| Type  | Name               | Description           |
|-------|--------------------|-----------------------|
| int32 | Right child offset | Offset to right child |
| int32 | Hull offset        | Offset to hull        |
| float | Center X           | Bounding box center   |
| float | Center Y           |                    |
| float | Center Z           |                    |
| float | Size               | Bounding box size     |
| uint8 | Scale X            | Size multiplier       |
| uint8 | Scale Y            |                    |
| uint8 | Scale Z            |                    |
| uint8 | Padding            | 0                     |

-   Right child offset is relative to itself and is 0 for end points.
-   Hull offset is relative to itself, negative number, and 0 for
    branches.
-   Read axis multiplier components as unsigned byte, divide 0xFA and
    scale bounding box size by them.
-   Meshes with only one hull will have no shrinkwrap and only one node.

### Hardpoints Section

| Type   | Name  | Description                       |
|--------|-------|-----------------------------------|
| uint32 | Tag   | 0x64697068 (hpid)                 |
| uint32 | Count | Humber of hardpoint IDs to follow |

Contains list of flcrc32s of hardpoint names. The list indicates which
hardpoints will have their own hitbox overridden by hull of same ID
provided in part meshes. Otherwise attached objects will retain their
own hitboxes.

Vanilla models used boxes for HpWeapon hardpoints, hemispheres for
HpTurret hardpoints, and some sort of cylinder for equipment hardpoints
such as HpCM, HpThrust.

Treewyrm's Notes
----------------

Here are some personal notes and observations regarding the format,
which may or may not be correct.

For completeness sake despite opposite face index not being used it
could be done in MAX by firing ray from triangle center opposite to face
normal and see which face it lands on.

It's most likely that hulls within part matching it's child partID
overrides any hulls of a child part. There are few "broken" hitboxes in
Freelancer, for example or\_elite.sur where hulls of wing parts
(Or\_port\_wing\_lod1 and Or\_star\_wing\_lod1) are misplaced, yet Root
has hulls for them at correct place.

Hulls of child partID are definitely used as can be observed with case
of depot\_lod.sur where tube walls hulls are stored within Root part yet
have partID of two children (port\_dock\_lod1 and star\_dock\_lod1) and
not Root.

For Lanceredit it might be useful to display hulls in color matching
designated color of a part and use some special shading to hulls whose
partID doesn't match part or any of its descendants (and their
hardpoints too!) with a message in log telling which hull and where has
no part to be associated with.
