---
title: BSZ
---

\[Lazy Bastard\]: Using a hex editor, I've mapped out a BSZ file, namely WM0.BSZ - Cloud's world map model. Incidentally, WM1.BSZ is Tifa's world map model, WM2.BSZ is Cid's, and WM3.BSZ seems to be every other world map model, similar to a BSX in fields (though I haven't confirmed this yet).

There is a remarkable resemblance here to BCX format, so I should be able to create custom world map models fairly soon, using the same techniques I used in fields. \[Update: I did just that; see Qhimm.com forum threads\]

Akari: Perhaps you can use this data to implement a utility and functionality in Q-Gears?

Without further ado:

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

In PSX memory, WM0.BSZ starts at 14A600 (and ends at 14F3B7). Every 80-type offset in this file can be treated as such: offset minus 8014A600 equals in-file offset.

<b>'BSZ Header Section'</b> \[at offset 0x00000000\]:

Unknown (always the same) \[at offset 0x00000000\]:

`02 00 00 00 08 00 00 00`

Relative offset from end of 'BSZ Header Section' to 'Texture Data Section' \[at offset 0x00000008\]:

`5C 47 00 00`

Offset to Model Section, Header (offset minus 0x8014A600 equals file offset) \[at offset 0x00000010\]:

`18 A6 14 80`

Relative offset from end of 'BSZ Header Section' to Unknown \[at offset 0x00000014\]:

`54 47 00 00`

Note: 'BSZ Header Section' runs until 'Model Section'.

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

<b>'Model Section'</b> \[at offset 0x00000018\]:

<b><i>'Model Section', Header</i></b> \[at offset 0x00000018\]:

`01 FF 16 0F 0A 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 02 58 00 38 02 3C A6 14 80 00 00 00 00`

01 FF - Unknown

16 - Number of bones

0F - Number of parts

0A - Number of animations

00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 02 58 00 - Unknown

38 02 - Total data size of bones+parts (little-endian, so 0x0238)

3C A6 14 80 - Offset to 'Model Section', Bones (little-endian, so 0x8014A63C)

00 00 00 00 - Unknown

Note: 'Model Section', Header runs until 'Model Section', Bones.

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

<b><i>'Model Section, Bones'</i></b> \[at offset 0x0000003C\]:

`00 00 FF 00 00 00 00 01 CB FF 01 01 7B FF 02 01 CB FF 01 00 82 FF 04 00 BA FF 05 01 B7 FF 06 01 74 FF 07 01 CB FF 01 00 82 FF 09 00 BA FF 0A 01 B7 FF 0B 01 74 FF 0C 01 00 00 00 00 C4 FF 0E 01 4F FF 0F 01 39 FF 10 01 00 00 00 00 C4 FF 12 01 4F FF 13 01 39 FF 14 01`

Note: 'Model Section', Bones runs until 'Model Section', Parts.

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

<b><i>'Model Section, Parts'</i></b> \[at offset 0x00000094\]:

`01 01 0E 00 00 00 00 00 00 00 0C 06 00 00 74 00 AC 01 AC 01 AC 01 28 02 14 A9 14 80 00 00 00 00 01 02 20 00 00 00 00 00 00 00 06 1B 00 00 04 01 80 03 80 03 80 03 74 04 C0 AA 14 80 00 00 00 00 01 03 5A 0C 02 06 00 00 00 00 96 0D 03 08 D4 02 EC 0D E0 0D 04 0E 94 13 40 AE 14 80 00 00 00 00 01 06 10 00 00 00 00 00 00 00 0A 09 00 00 84 00 D8 01 D8 01 D8 01 5C 02 4C BC 14 80 00 00 00 00 01 07 10 00 00 00 00 00 00 00 00 0E 00 00 84 00 9C 01 9C 01 9C 01 F8 01 24 BE 14 80 00 00 00 00 01 08 08 00 00 00 00 00 00 00 00 06 00 00 44 00 BC 00 BC 00 BC 00 D8 00 C0 BF 14 80 00 00 00 00 01 0B 10 00 00 00 00 00 00 00 0A 09 00 00 84 00 D8 01 D8 01 D8 01 5C 02 7C C0 14 80 00 00 00 00 01 0C 10 00 00 00 00 00 00 00 00 0E 00 00 84 00 9C 01 9C 01 9C 01 F8 01 54 C2 14 80 00 00 00 00 01 0D 08 00 00 00 00 00 00 00 00 06 00 00 44 00 BC 00 BC 00 BC 00 D8 00 F0 C3 14 80 00 00 00 00 01 0F 0A 00 00 00 00 00 00 00 08 04 00 00 54 00 24 01 24 01 24 01 70 01 AC C4 14 80 00 00 00 00 01 10 15 00 00 00 00 00 00 00 04 0E 00 00 AC 00 04 02 04 02 04 02 68 02 D0 C5 14 80 00 00 00 00 01 11 0A 00 00 00 00 00 00 00 02 07 00 00 54 00 00 01 00 01 00 01 34 01 D4 C7 14 80 00 00 00 00 01 13 0A 00 00 00 00 00 00 00 08 04 00 00 54 00 24 01 24 01 24 01 70 01 D4 C8 14 80 00 00 00 00 01 14 15 00 00 00 00 00 00 00 04 0E 00 00 AC 00 04 02 04 02 04 02 68 02 F8 C9 14 80 00 00 00 00 01 15 0A 00 00 00 00 00 00 00 02 07 00 00 54 00 00 01 00 01 00 01 34 01 FC CB 14 80 00 00 00 00`

Breakdown of first line of 'Model Section', Parts above:

01 - Unknown; "0 - not calculate stage lighting and color. 1 - calculate."

01 - Bone to which this part is attached to

0E - Number of vertices

00 - Number of Texture coord

00 - number of textured quads (Gourad Shading)

00 - number of textured triangles (Gourad Shading)

00 - number of textured quads (Flat Shading)

00 - number of textured triangles (Flat Shading)

00 - number of monochrome triangles

00 - number of monochrome quads

0C - number of gradated triangles

06 - number of gradated quads

00 00 - number of data in block 4 (flags)

74 00 - Relative offset to ?

AC 01 - Relative offset to ?

AC 01 - Relative offset to texture settings. Indexed by 5th block data (control)

AC 01 - Relative offset to one byte stream for every packet with texture

28 02 - Relative offset to ?

14 A9 14 80 - Offset to skeleton data section (little-endian, so 0x8014A914)

00 00 00 00 - Offset to ?

Note: 'Model Section', Parts runs until 'Model Section', Animations.

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

<b><i>'Model Section', Animations</i></b> \[at offset 0x00000274\]:

`01 00 16 00 02 00 B4 00 B4 00 B8 00 FC CC 14 80 0F 00 16 01 01 20 B4 00 D2 00 D4 00 B4 CD 14 80 14 00 16 03 00 26 B4 00 2C 01 2C 01 68 D0 14 80 18 00 16 03 00 33 B4 00 44 01 44 01 8C D4 14 80 0F 00 16 03 00 39 B4 00 0E 01 0E 01 98 DA 14 80 0F 00 16 03 00 34 B4 00 0E 01 0E 01 00 DF 14 80 1E 00 16 00 02 1E B4 00 B4 00 B8 00 1C E3 14 80 1E 00 16 00 02 01 B4 00 B4 00 B8 00 58 E7 14 80 0F 00 16 02 00 2A B4 00 F0 00 F0 00 30 E8 14 80 06 00 16 01 02 2B B4 00 C0 00 C4 00 98 EB 14 80`

Note: In CLOUD.BCX, the above would be the end of the file. In this format, however, the Model Section

(with all its components) comes before actual parts/animations data, after which is texture data, which

comprises the remainder of the file.

Note: 'Model section', Animations runs into 'Skeleton Data Section'.

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
