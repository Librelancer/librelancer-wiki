VMeshLibrary
============

VMeshLibrary stores one or more uniquely named VMeshData entries.

VMeshData
---------

VMeshData is pretty much a big vertex/index buffer that contains all
vertices and triangles of one or multiple parts. However when containing
meshes of multiple parts VMeshData isn't directly aware of how many has,
it's a dumb storage, but an indirect indication that another part begins
within its data set is when VMeshGroup.vertexStart is 0 and when vertex
index in triangle also starts from 0. Normally parts never share
vertices, but mesh groups within a part may.

VMeshRef points to range of indices, vertices and mesh groups.

### Header

| Offset | Type   | Name           | Description                                                   |
|--------|--------|----------------|---------------------------------------------------------------|
| 0x00   | uint32 | Mesh type      | Always 1, game crashes otherwise.                             |
| 0x04   | uint32 | Primitive type | Direct3D primitive type. Default is 4 which is triangle list. |
| 0x08   | uint16 | Group count    | How many individual mesh groups contained there.              |
| 0x0A   | uint16 | Indices count  | How many indices are used for triangle buffer.                |
| 0x0C   | uint16 | FVF            | Direct3D Flexible Vertex Format.                              |
| 0x0E   | uint16 | Vertex count   | How many vertices stored here.                                |

Primitive type can be one of the following:

1.  Points
2.  Line List
3.  Line Strip
4.  Triangle List
5.  Triangle Strip
6.  Triangle Fan

Followed by VMeshGroups.

### VMeshGroup

A group is a collection of vertices and vertex indices forming triangles
that are grouped by a single material identifier. It is fairly common in
Freelancer for a model or even an individual part of a compound model to
use multiple materials. So a model or each of its parts may use one more
groups if they use multiple materials.

| Offset | Type   | Name          | Description                                                  |
|--------|--------|---------------|--------------------------------------------------------------|
| 0x00   | uint32 | Material ID   | FL32CRC of material name.                                    |
| 0x04   | uint16 | Start index   | Vertex buffer index range defined by start and end.          |
| 0x06   | uint16 | End index     |                                                           |
| 0x0A   | uint16 | Indices count | How many indices are used in triangle buffer for this group. |
| 0x0C   | uint16 | Padding       | Always 0xCC                                                  |

Followed by triangle buffer and then by vertex buffer.

Parts
=====

Individual parts of compound mesh are usually stored as *whatever.3db*
entry in UTF root, sometimes name contains timestamp, sometimes has lod0
added to as well, but ultimately the name of entry is irrelevant so long
as it's not one of the other common ones that would confuse loading
procedure. This entry name is referred within *File name* node in parts
listing within *Cmpnd*.

VMeshPart/VMeshRef
------------------

Contained within *VMeshPart* it has no mesh data of its own and instead
refers to specific *VMeshData* by CRC of entry name and points to ranges
of vertices, indices and mesh groups that would form up the mesh of the
individual part.

It is possible for multiple *VMeshRefs* to point to exact same ranges of
the same *VMeshData*, which allows to save up some space and introduces
re-usable instanced geometry where multiple parts of the model may use
exactly same geometry but via joint transformation located in different
positions.

VMeshRef may also use 0 vertices, indices and mesh groups making such
part by itself an invisible dummy. This can be used to create proxy
parts to provide additional axis or types for constrained joint
animation.

| Offset | Type   | Name                | Description                                                             |
|--------|--------|---------------------|-------------------------------------------------------------------------|
| 0x00   | uint32 | Size                | VMeshRef size. It's always 60 bytes.                                    |
| 0x04   | uint32 | VMeshData reference | Points to VMeshData entry (internally or externally) by its hashed name |
| 0x08   | uint16 | Vertex start        | Start vertex index in vertex buffer.                                    |
| 0x0A   | uint16 | Vertex count        | How many vertices in vertex buffer are used.                            |
| 0x0C   | uint16 | Index start         | Start triangle vertex index in triangle buffer.                         |
| 0x0E   | uint16 | Index count         | How many vertex indices in triangle buffer are used.                    |
| 0x10   | uint16 | Group start         | Start mesh group.                                                       |
| 0x12   | uint16 | Group count         | How many mesh groups are used.                                          |
| 0x14   | float  | Max X               | Bounding box corner vertices.                                           |
| 0x18   | float  | Min X               |                                                                      |
| 0x1C   | float  | Max Y               |                                                                      |
| 0x20   | float  | Min Y               |                                                                      |
| 0x24   | float  | Max Z               |                                                                      |
| 0x28   | float  | Min Z               |                                                                      |
| 0x2C   | float  | Center X            | Bounding sphere center offset.                                          |
| 0x30   | float  | Center Y            |                                                                      |
| 0x34   | float  | Center Z            |                                                                      |
| 0x38   | float  | Radius              | Bounding sphere radius.                                                 |

Multilevel
----------

Each part may have several LOD meshes independent of other parts in
mod*VMeshPart* /el. *VMeshRef* then are used within *Level* entries
indicating LOD and *Switch2* entry specifying ranges of each level.
*Switch2* is often overridden within INI files with *LODRanges* property
instead but will be used in its absence.

VMeshWire/VWireData
-------------------

Contains a list of vertex index pairs forming sequence of lines drawn
between the pairs. Wire mesh typically is used in HUD for inspecting and
selecting subparts of the currently targeted object. Other than using
vertex indices lines are completely arbitrary and don't have to follow
original mesh geometry or its actual edges. Wire mesh is purely for
schematic visualization.

*VWireData* can refer to any *VMeshData* and not necessarily the same
one, often times it refers to farthest LOD for fewer lines to draw. You
could even use vertices in *VMeshData* that aren't even used in any
*VMeshRefs* to draw something completely different.
