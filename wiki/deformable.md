Deformable Meshes
=================

While stored in UTF format like most other assets they have a different
structure to rigid meshes (VMesh).

Deformable Model
----------------

Deformable models are always compound, consist of multiple parts
representing bones of skeleton. Skeleton hierarchy is defined by Cmpnd
much like rigid compound models are.

MultiLevel
----------

Mesh data is stored within MultiLevel entry located in UTF root entry.

Deformable meshes aren't shareable between files, meshes defined within
.dfm file are in local scope.

Single-part rigid meshes also happen to use entry of the same name to
store VMeshPart/VMeshRefs for each LOD, however contents of MultiLevel
for deformable mesh are different. Luckily you can avoid mixing the two
by checking presence of Cmpnd within UTF as well, as rigid model has
either Cmpnd or MultiLevel but never both which is the case only for
deformable models.

-   Fractions
-   Mesh\*
    -   Face\_groups
        -   Count
        -   Group\*
            -   Material\_name
            -   Tristrip\_indices
            -   Face\_indices
    -   Geometry
        -   Point\_indices
        -   UV0\_indices
        -   UV1\_indices
        -   Points
        -   Point\_bone\_first
        -   Point\_bone\_count
        -   Bone\_id\_chain
        -   Bone\_weight\_chain
        -   Vertex\_normals
        -   UV0
        -   UV1
        -   Min\_du
        -   Max\_du
        -   Min\_dv
        -   Max\_dv
        -   Bone\_X\_to\_U\_scale
        -   Bone\_Y\_to\_V\_scale
        -   UV\_bone\_id
        -   UV\_vertex\_count
        -   UV\_vertex\_id
        -   UV\_default\_list
        -   UV\_plane\_distance

| Entry                 | Type   | Name               | Description                                                                                             |
|-----------------------|--------|--------------------|---------------------------------------------------------------------------------------------------------|
| Fractions             | float  | Level of Detail    | LOD fractions from range defined in INIs, amount of floats indices how many Mesh\* entries are expected |
| Mesh\*                | folder | Mesh LOD           |                                                                                                         |
| Face\_groups          | folder | Face groups        |                                                                                                         |
| Count                 | uint32 | Face group counter | Indicates how many Group\* are to follow                                                                |
| Group\*               | folder | Face group         |                                                                                                         |
| Material\_name        | string | Group material     | Name of material to use for this group of mesh faces                                                    |
| Tristrip\_indices     | uint16 | Triangle strip     | Vertex indices of triangles strip                                                                       |
| Face\_indices         | uint16 | Triangle array     | Vertex indices of triangles array                                                                       |
| Geometry              | folder | LOD geometry       |                                                                                                         |
| Point\_indices        | uint32 | ?                  | ?                                                                                                       |
| UV0\_indices          | uint32 | ?                  | ?                                                                                                       |
| UV1\_indices          | uint32 | ?                  | ?                                                                                                       |
| Points                | float  | Vertex coordinates | 3x float per vertex                                                                                     |
| Point\_bone\_first    | uint32 | ?                  | ?                                                                                                       |
| Point\_bone\_count    | uint32 | ?                  | ?                                                                                                       |
| Bone\_id\_chain       | uint32 | ?                  | ?                                                                                                       |
| Bone\_weight\_chain   | uint8  | ?                  | 4x per bone?                                                                                            |
| Vertex\_normals       | float  | Vertex normals     | 3x float per vertex                                                                                     |
| UV0                   | float  | Map 0 UV           | UV coordinates (2xfloat)                                                                                |
| UV1                   | float  | Map 1 UV           | UV coordinates (2xfloat)                                                                                |
| Min\_du               | float  | Min U Delta        | Single float                                                                                            |
| Max\_du               | float  | Max U Delta        | Single float                                                                                            |
| Min\_dv               | float  | Min V Delta        | Single float                                                                                            |
| Max\_dv               | float  | Max V Delta        | Single float                                                                                            |
| Bone\_X\_to\_U\_scale | float  | Bone X to Map 1 U  | Bone X position translation scales to Map 1 U                                                           |
| Bone\_Y\_to\_V\_scale | float  | Bone Y to Map 1 V  | Bone Y position translation scales to Map 1 V                                                           |
| UV\_bone\_id          | uint32 | Bone ID            | Bone which affects selected vertices UV                                                                 |
| UV\_vertex\_count     | uint32 | Vertex count       | UV\_vertex\_id?                                                                                         |
| UV\_vertex\_id        | uint32 | Vertex IDs         | Vertices affected by bone at UV\_bone\_id                                                               |
| UV\_default\_list     | float  | UVs                | Default UV coordinates for vertices in UV\_vertex\_id?                                                  |
| UV\_plane\_distance   | float  | ?                  | Default 1?                                                                                              |
