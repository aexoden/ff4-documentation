Data Structures
===============

This chapter details the various data structures used by the game.

Character Records
-----------------

Character records are stored in two separate areas in RAM. The first set is
made up of five 64-byte records located at $7E1000. The second set of is five
128-byte records located at $7E2000. The second set of records is used
in-battle and is a strict superset of the first set. The individual bytes can
be interpreted as follows:

$01: Character Byte 1
^^^^^^^^^^^^^^^^^^^^^

:Bitmask: %RL?CCCCC
:R: Right-hand weapons
:L: Left-hand weapons
:C: Character ID

The lowest five bits define the character ID. Therefore, the game supports up
to :math:`2^5` or 32 unique character IDs. One of these is reserved for
indicating an empty slot. Each instance of a character has its own ID. (For
example, Kain leaves the party and rejoins twice. All three instances of Kain
have a separate ID. The values used in game are as follows:

=== ======================================
ID  Character
=== ======================================
$00 <empty slot>
$01 Cecil (dark knight)
$02 Kain (until Mist)
$03 Rydia (child)
$04 Tellah (until Damcyan)
$05 Edward (until Leviatan)
$06 Rosa (until Fabul)
$07 Yang (until Leviatan)
$08 Palom
$09 Porom
$0A Tellah (until Cecil becomes a Paladin)
$0B Cecil (paladin)
$0C Tellah (until Tower of Zot cutscene)
$0D Yang (until Super Cannon)
$0E Cid
$0F Kain (until Sealed Cave)
$10 Rosa (final)
$11 Rydia (adult)
$12 Edge
$13 FuSoYa
$14 Kain (final)
$15 Golbez
$16 Anna
=== ======================================

The two highest bits determine the character's handedness. If the uppermost bit
is set, the character can equip weapons on their right hand, and if the second
highest bit is set, they can equip weapons on their left hand.

The remaining bit's function is currently unknown.

$02: Character Byte 2
^^^^^^^^^^^^^^^^^^^^^

:Bitmask: %B?L?SSSS
:B: Back row
:L: Long range
:S: Sprite/Class

The lowest four bits determine the sprite and class of the character. These two
properties are inextricably tied together and are defined using one value. The
known values are as follows:

=== =================== =========
ID  Sprite              Class
=== =================== =========
$00 Cecil (dark knight) DKnight
$01 Kain                Dragoon
$02 Rydia (child)       Caller
$03 Tellah              Sage
$04 Edward              Bard
$05 Rosa                Wh.Wiz
$06 Yang                Karate
$07 Palom               Bl.Wiz
$08 Porom               Wh.Wiz
$09 Cecil (paladin)     Paladin
$0A Cid                 Chief
$0B Rydia (adult)       Caller
$0C Edge                Ninja
$0D FuSoYa              Lunar
$0E \*Various           <garbage>
$0F \*Golbez            <garbage>
=== =================== =========

$0E appears to be garbage data and is not actually used in the game. The
portrait is that of a solid black pig, the overworld sprite is a mini and the
in-battle sprite is a green pig. $0F provides an in-battle sprite of Golbez,
but the rest of the data is not usefully defined.

Bit 5 is the so-called long range bit, which determines whether or not the
character is capable of performing long range attacks without having their
accuracy reduced. This bit is especially notable because the game has a bug
that results in this bit never being reset. In other words, once a character
equips a back row weapon (and the game is given an opportunity to recalculate
stats), the character will retain that status forever.

Bit 7 is the back row bit and is set when the character is in the back row and
unset when the character is in the front row. This does not affect the display
of party members and appears to be used only for calculations.

The functions of the remaining two bits are currently unknown.
