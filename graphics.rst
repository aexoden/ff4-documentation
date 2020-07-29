Graphics
========

This chapter discusses the location and formats of the various forms of
graphical data used in the game.

Field Sprites
-------------

Field sprites are those used on the field. In particular, they are generally
used to display the player and the NPCs.

Player
~~~~~~

The field sprites for party members are made up of several components. First,
the actual graphical data is stored in $300 byte arrays at $1B:8000. Each sheet
is composed of 32 8x8 tiles. The data is stored with 3 bits per pixel.

These sheets are composed into 16 different frames using the OAM flags stored at
$15:C0C4. Each frame has eight bytes of data, where the even bytes are the tile
numbers for the upper left, upper right, lower left and lower right component
tiles, respectively, and the odd bytes are the tile flags for the same tiles.
In the default data, the flags are used to set the horizontal flip bit if
appropriate and to set the priority for each tile to 2.

The coordinates for the four tiles of the player sprite are determined by the
data at $15:C0B4, which consists of four bytes per tile (corresponding to the
full OAM entry). Only the X and Y coordinates are set, with both the flags and
tile bytes being set to zero. Note that the base coordinates for the player
sprite are 112, 109, which is neither centered nor exactly aligned with a tile
boundary. In addition, on the second frame of horizontal movement, the Y
coordinate is reduced further by one.

The palette for each class's sprite is stored in the array at $15:B2FA. Each
class receives one byte, which determines which of the four player sprite
palettes to use.

The palettes themselves are stored at $0D:8000, which consists of four separate
eight color palettes, with each palette potentially being shared by multiple
characters (only the paladin Cecil palette is unique).

Title Screen
------------

The title screen is a simple static image displayed at the start of the game. Do
note that the title screen in the original Japanese release has some form of
animation. That is not currently documented here.

Tiles
~~~~~

The title screen tile data is stored with 4 bits per pixel at $08:C000. There is
room for 256 tiles, though not all 256 are used in this release.

Tilemap
~~~~~~~

A standard SNES tilemap is stored in the 2048 bytes starting at $08:E000. The
data is sufficient to render a 256x256 image.

Palette
~~~~~~~

The eight 16-color palettes used by the screen are stored at $08:E800.

Outdoor Maps
------------

Outdoor maps are composed from a set of 128 16x16 composed tiles, each
created by drawing from a pool of 256 8x8 base tiles.

Base Tiles
~~~~~~~~~~

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

The tables for the overworld and underworld are both 8192 bytes each, but the
table for the moon is only 5056 bytes (158 tiles instead of 256).

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

Composed Tiles
~~~~~~~~~~~~~~

The 16x16 composed tiles are composed from the 8x8 tiles via the data in the
following tables:

========== ========
Map        Address
========== ========
Overworld  $14:8000
Underworld $14:8200
Moon       $14:8400
========== ========

Each 512 byte block is actually composed of four separate 128-byte arrays. The
four arrays, in order, specify the tiles to use in the upper left, upper right,
lower left and lower right segments of the composed tile, in that order. In
other words, the first 128 bytes determine the upper left tile for each of the
128 composed tiles, and so on.

Tilemaps
~~~~~~~~

The tilemaps for the outdoor maps are stored in a slightly compressed form at
the following addresses:

========== =========== ==================
Map        Row Offsets Compressed Tilemap
========== =========== ==================
Overworld  $16:8000    $16:8480
Underworld $16:8200    $16:C480
Moon       $16:8400    $16:E180
========== =========== ==================

The row offset data is a series of 16-bit offsets for each row of tiles,
allowing one to index directly to a particular row (which allows the game to
avoid loading all rows into memory at once, as that would be extremely
expensive in terms of memory usage.

The actual compressed tilemap data is a mostly basic run-length encoding scheme.
If the high bit of the value is not set, the value is directly copied to the
tilemap, except potentially for the values of $00, $10, $20 and $30. In those
cases, if the map is the overworld, a four byte sequence will instead be
written. The first byte in the sequence is the specified value. The following
three bytes are equal to $70 plus the value divided by 16 and multiplied by 3
plus either 0, 1 or 2.

If the high bit of the value is set, the low seven bits determine the tile
number, and the following byte is the number of tiles to write minus one. (This
allows a value of $FF, for instance, to encode a full row of 256 tiles.)

All tile numbers refer to the 16x16 composed tiles.

Each row of data in the compressed tilemap is additionally terminated with a
single $FF.

Animation
~~~~~~~~~

There are two classes of animated outdoor tiles: ocean tiles and waterfall
tiles. Both of these are animated in similar fashions, with custom code directly
manipulating the tiles as stored in RAM.

For the ocean tiles, every other frame, a row selected based on a frame counter
and the data table at $00:8E8C is rotated to the right, with each pixel moving
once to the right, except for the final pixel, which is rotated back to the far
left. Within the tiles, the ocean tiles make up tiles $80 through $83.

For the waterfall tiles, once per frame, a column selected via the frame counter
and the data table at $00:8E7C is rotated downward, though the process is
repeated so each pixel moves two spots per frame. The waterfall tiles range from
$7A to $7D.
