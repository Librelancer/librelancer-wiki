# RDL Text

Infocards stored in Freelancer resource .dlls are stored in an XML format referred to as RDL. These are encoded as UTF-16 text with a BOM. 
 
Sample infocard:
 
 ```
<?xml version="1.0" encoding="UTF-16"?>
<RDL>
    <PUSH/>
    <TEXT>Welcome to the Librelancer infocard example XML!</TEXT>
    <PARA/>
    <TEXT>This is the second paragraph</TEXT>
    <POP/>
</RDL>
```

RDL format takes a series of XML elements as sequential instructions.

## Elements

### `PUSH`
TODO, should be present at start of RDL

### `POP`
TODO, should be present at the end of RDL

### `TEXT`
Adds the text between `<TEXT>` and `</TEXT>` to the rich text with the current attributes.

### `JUST`
Changes the text alignment, in format `<JUST LOC="alignment"/>` where accepted values are `LEFT, RIGHT, CENTER`

### `PARA`
Paragraph, adds a line break. Similar to HTML `<p>`

### `TRA`
Text Render Attributes, use this to change the font style, text colors etc.
This comes in two forms.

**Packed Form**

The first packs the data into integers using a bitfield, e.g:`<TRA DATA="1" MASK="1" DEF="0"/>` will set the font to bold. 
`DATA` contains the values, `MASK` contains which values are used, and `DEF` serves as `MASK` but takes default values.
Valid bitfield values are:

| Name | Value | Hex |
|-----------|-------|-|
| `BOLD`    | `1`   | `0x1` |
| `ITALIC`  | `2`   | `0x2` |
| `UNDERLINE` | `4` | `0x4` |
| `FONT`    |  `3720` | `0xF8` |
| `COLOR`   | `-256` | `0xFFFFFF00` |

The XML tags only store signed decimal 32-bit integers, if editing manually it is best to do bitwise in hex then convert to decimal for the tag.
Colours are stored: BBGGRR00.

**Example**:
Red, bold text

DATA = `Bold (0x1) | Red (0x1D1DBF00) = 0x1D1DBF01`
MASK = `Bold (0x1) | Color (0xFFFFFF00) = 0xFFFFFF01`

Converted to signed decimal 32-bit integers this gives us the tag `<TRA DATA="488488705" MASK="-255" DEF="0"/>`

**Unpacked Form**

This is a more standard form, where the TRA tag has data in individual attributes.

Those attributes are:

| Name    | Accepted Values |
|---------|-----------------|
| `COLOR` | `fuchsia, gray, blue, green, aqua, red, yellow, white` OR `#FFF, #FFFFFF` hex colors |
| `BOLD` | `TRUE, FALSE, DEFAULT` |
| `ITALIC` | `TRUE, FALSE, DEFAULT` |
| `UNDERLINE` | `TRUE, FALSE, DEFAULT` |
| `FONT` | Numeric font index |

Font index for both forms refers to the `[TrueType]` section in rich_fonts.ini.

An example entry being

```ini
[TrueType]
font = 0, Arial, 22
font = 1, Times New Roman, 35
```











. `
