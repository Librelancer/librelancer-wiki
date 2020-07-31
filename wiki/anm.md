# Animation (.anm) Files

Animation files are a collection of
scripts (defined by \`/Animation/Script\` node)

Script
======

The script node contains a collection of animation scripts by name,
which can have a number of object and joint maps.

Root Height
-----------

`float` describing \_\_\_\_

Object Map
----------

??

Joint Map
---------

### Parent Name

Parent Name of joint as specified in Cmpnd

### Child Name

Child Name of joint as specified in Cmpnd

### Channel

#### Header

-   `int32` Number Of Frames
-   `float` Interval
-   `int32` Type

| Value | Type                                |
|-------|-------------------------------------|
| 1     | Single angle for joint to rotate to |
| 4     | Quaternion                          |
| 128   | ?? - Found in Dfm                   |
| 144   | ?? - Found in Dfm Object Maps       |
