# Universal Tree Format

Binary format used by most content assets in Freelancer. Stores hierarchical structure with named nodes (called entries) and arbitrary fixed length binary content. Not too dissimilar to XML but significantly simpler.

## Header

Header is at least 56 bytes.

| Offset | Type | Name | Description |
|-|-|-|-|
0x00 | uint32 | Signature | FourCC signature. Must always be 0x20465455 (UTF ).
0x04  | uint32  | Version  | UTF version. Must always be 0x101.
0x08  | uint32  | Tree offset  | Typically 56 as tree follows right after header.
0x0C  | uint32  | Tree size  | Bytes used by tree. Divide by entry size to get number of entries in tree.
0x10  | uint32  | Unused entry offset  | Skip reading entries to offset, usually is 0.
0x14  | uint32  | Entry size  | Byte length of single entry record in tree.
0x18  | uint32  | Names offset  | Location of entry names dictionary.
0x1C  | uint32  | Names allocated size  | Bytes pre-allocated for names.
0x20  | uint32  | Names used size  | Bytes actually used by dictionary.
0x24  | uint32  | Data start offset  | Entry values start offset 


- Entry size value must be 44, otherwise Freelancer will not process the file and in some cases crash.
- Unused offset and size are non-zero in few Freelancer files where the nodes seem were removed yet entry data remained. An example of such are several fighter ships shield .3db files.

## Entry

Usually 44 bytes

| Offset  | Type  | Name  | Description |
|-|-|-|-|
0x00  | uint32  | Next sibling offset  | 
0x04  | uint32  | Dictionary name offset  | 
0x08  | uint32  | Entry type  | Directory or file (see: Win32 API dwFileAttributes)
0x0C  | uint32  | Sharing attributes  | 
0x10  | uint32  | Child or data offset  | Offset to child entry if type is 0x80 or to data if type is 0x10
0x14  | uint32  | Allocated data size  | Allocated data byte length.
0x18  | uint32  | Used data size  | Actually used data byte length.
0x20  | uint32  | Uncompressed data size  | Same as above.
0x24  | uint32  | Creation time  | 
0x28  | uint32  | Last access time  | 
0x2C  | uint32  | Last write time  | 

- In theory timestamps can be cut to save some space for UTFs with a lot of entries. The game doesn't mind.

# Names

Dictionary of names is simply a sequence of NUL terminated C-strings. Characters are limited to ASCII range. 

- In Freelancer UTFs first byte is always 0. Entries present in tree but no longer referenced (marked for deletion?) have their name offsets at 0 as well.

# Alignment

Freelancer UTFs do 32-bit alignment by adding padding to names dictionary and to each data if necessary. The game however does not require alignment and can work with non-aligned UTFs.
Creating non-aligned UTFs will cause certain community-made modding tools to fail
