Graphics
========

This chapter discusses the location and formats of the various forms of
graphical data used in the game.

Outdoor Maps
------------

The outdoor maps are implemented via mode 7, so each pixel is represented by a
single byte, which directly indexes the palette data in CG-RAM.

Each 8x8 tile uses a single 16-color palette, with the upper four bits of each
pixel in that tile determined by the data in the following table, where each
8x8 tile is one byte (even though only the upper four bits are used):

========== ========
Map        Address
========== ========
Overworld  $14:8600
Underworld $14:8700
Moon       $14:8800
========== ========

The lower four bits of each pixel's value are loaded from a separate table:

========== ========
Map        Address
========== ========
Overworld  $1D:8000
Underworld $1D:A000
Moon       $1D:C000
========== ========

In this table, two pixels are defined per byte, with the first pixel of each
pair using the lower four bits, and the second pixel using the upper four bits.
These four bits are extracted, shifted to the correct location, and combined
with the previously-specified bits for the tile to determine the final index
into the palette.

The palette data itself is standard SNES palette data, loaded from the offset
listed in the following table:

========== ========
Map        Address
========== ========
Overworld  $14:8900
Underworld $14:8980
Moon       $14:8A00
========== ========

Each palette consists of 128 bytes, making up 64 colors.
