# TXM File Format

## Overview

A **`.txm` file** is a **UTF container** used by *Freelancer* (and supported by **LibreLancer**) to store **animated 2D textures**.

Unlike MAT files, TXM files are **self-contained**:
- They embed the texture data directly
- They store animation metadata
- They define how animation frames are sampled from the textures

TXM files are commonly used for:
- UI animations
- HUD elements
- Animated effects (e.g. engine glows, indicators)

---

## UTF Structure Overview

A typical TXM file has the following structure:

```
/
└─ Texture library
├─ <TextureName>_0
│ ├─ MIP0
│ ├─ MIP1
│ ├─ MIP2
│ ├─ MIP3
│ ├─ MIP4
│ ├─ MIP5
│ ├─ MIP6
│ ├─ MIP7
│ └─ MIP8
└─ <TextureName>
├─ Texture count
├─ Frame count
├─ FPS
└─ Frame rects
```

---

## Texture Library

The **Texture library** node contains all texture and animation data used by the TXM.

Each animated texture consists of:
- One texture data branch (`<TextureName>_0`)
- One animation metadata branch (`<TextureName>`)

---

## Texture Data (`<TextureName>_0`)

This node stores the **actual texture pixel data**.

### MIP Nodes

- Each `MIPx` node contains a **byte array**
- The byte array stores the raw texture data for that mip level
- MIP indices typically range from **MIP0** (highest resolution) to **MIP8** (lowest resolution)

The texture format is implicit and interpreted by the engine.

---

## Animation Metadata (`<TextureName>`)

This node defines how the embedded texture(s) are animated.

### Texture count

- Type: `int32`
- Number of textures used by the animation
- Usually `1`, but multiple textures are supported

---

### Frame count

- Type: `int32`
- Total number of animation frames
- Must match the number of entries in **Frame rects**

---

### FPS

- Type: `float`
- Playback speed of the animation
- Defines how many frames are displayed per second

---

## Frame Rects

The **Frame rects** node defines where each animation frame is located inside the texture(s).

Each entry corresponds to **one animation frame**, and entries are processed in order.

### Frame Rect Entry Layout

Each frame rect entry is **20 bytes**, stored in little-endian format:

| Offset | Type   | Name          | Description |
|------:|--------|---------------|-------------|
| 0x00  | uint32 | TextureIndex  | Reserved / fixed value. Observed to always be `0`. however perhaps this can be used to have multipe textures as part of the animation - useful for modding purposes but not used in vanilla |
| 0x04  | float  | U0            | Left UV coordinate |
| 0x08  | float  | V0            | Top UV coordinate |
| 0x0C  | float  | U1            | Right UV coordinate |
| 0x10  | float  | V1            | Bottom UV coordinate |

---

## UV Coordinate System

- UVs are **normalized** (`0.0 – 1.0`)
- Coordinates follow a **top-left origin** convention:
  - `(U0, V0)` = top-left
  - `(U1, V1)` = bottom-right
- Frame dimensions are derived at runtime:
  - `Width  = U1 - U0`
  - `Height = V1 - V0`

The stored values do **not** represent pixel coordinates.

---

## Example Frame Rect (Hex)

Example frame rect using:
- `TextureIndex = 0`
- UV rectangle `(0.0, 0.0)` to `(0.25, 0.25)`

```
00 00 00 00 // TextureIndex = 0
00 00 00 00 // U0 = 0.0
00 00 00 00 // V0 = 0.0
00 00 80 3E // U1 = 0.25
00 00 80 3E // V1 = 0.25

```
## Animation Playback

- Frames are played sequentially in the order stored
- Playback speed is controlled by **FPS**
- TXM files contain no looping or timing logic beyond FPS
- Playback behavior is handled by the consuming system

## Index Field Notes

Although the frame rect entry begins with a `uint32`, analysis of genuine Freelancer TXM files shows:

- The value is always `0`
- It corresponds to the `_0` suffix of the texture data node
- It does **not** select between multiple textures
- Each animated texture in a TXM is defined independently

TXM files containing multiple textures do so by including multiple `<TextureName>` / `<TextureName>_0` pairs



## Multiple Animated Textures in a TXM

A single TXM file may contain **multiple independent animated textures**.

Each animated texture consists of:
- A texture data branch: `<TextureName>_0`
- An animation metadata branch: `<TextureName>`

These animations are independent and do not share frame data or textures.