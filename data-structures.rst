Data Structures
===============

This chapter details the various data structures used by the game.

Events
------

.. CAUTION::
   This section is a draft based on current reverse engineering work. Some of
   the information may be inaccurate or incomplete.

The game uses a simple virtual machine for scripting event sequences. The data
discovered for this so far exists in bank 12 ($12:XXXX on the system bus).
There are 256 event numbers. $12:8000 to $12:8200 is a series of 256 2-byte
indexes. These indexes subsequently indicate the beginning of each event's
script in the data array extending from $12:8200 to $12:EFFF. The event
commands have variable lengths depending on the nature of the command.

$FA: Play Song
^^^^^^^^^^^^^^

:Bytes: 2
:Parameter 1: Desired track number.

This instruction causes to play the song identified by the given track number.

$FE: Warp to Map
^^^^^^^^^^^^^^^^

:Bytes: \?
:Parameter 1: Target map ID.
:Parameter 2: %DDXXXXXX where DD is the target direction and XXXXXX is the
              target X coordinate.
:Parameter 3: Target Y coordinate.
:Parameter 4: %P??????? where P is the target map plane.

Map IDs from $00 to $FA act as expected and transport the player to that map.
Maps $FB to $FF have special behavior:

$FF: End Event
^^^^^^^^^^^^^^

:Bytes: 1

This instruction ends the event. It automatically makes sure the visible player
field sprite corresponds with the active character.


Character Records
-----------------

Character records are stored in two separate areas in RAM. The first set is
made up of five 64-byte records located at $7E1000. The second set of is five
128-byte records located at $7E2000. The second set of records is used
in-battle and is a strict superset of the first set. The individual bytes can
be interpreted as follows:

$00: Character Byte 1
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

$01: Character Byte 2
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

$02: Level
^^^^^^^^^^

:Bitmask: %LLLLLLLL
:L: Level

This is the character's level.

$03-$06: Status
^^^^^^^^^^^^^^^^^^

:Bitmask: %SSSSSSSS %SSSSSSSS %SSSSSSSS %SSSSSSSS
:S: Status effects

These bytes encode the following status effects:

==== === === ========= ======== ===========================
Byte Bit Hex Binary    In-Game  Description
==== === === ========= ======== ===========================
$03  7   $80 %10000000 Swoon    Swoon/Death
$03  6   $40 %01000000 Stone    Stone
$03  5   $20 %00100000 Toad     Toad
$03  4   $10 %00010000 Small    Small/Mini
$03  3   $08 %00001000 Pig      Pig
$03  2   $04 %00000100 Mute     Mute/Silence
$03  1   $02 %00000010 Darkness Darkness/Blindness
$03  0   $01 %00000001 Poison   Poison
==== === === ========= ======== ===========================

==== === === ========= ======== ===========================
Byte Bit Hex Binary    In-Game  Description
==== === === ========= ======== ===========================
$04  7   $80 %10000000 Curse    Curse
$04  6   $40 %01000000 Float    Float
$04  5   $20 %00100000 Paralyze Paralysis
$04  4   $10 %00010000 Sleep    Sleep
$04  3   $08 %00001000 Charm    Charm/Confuse
$04  2   $04 %00000100 Berserk  Berserk
$04  1   $02 %00000010 Petrify  Gradual petrification (2/3)
$04  0   $01 %00000001 D        Gradual petrification (1/3)
==== === === ========= ======== ===========================

==== === === ========= ======== ===========================
Byte Bit Hex Binary    In-Game  Description
==== === === ========= ======== ===========================
$05  7   $80 %10000000 D        Magnetized
$05  6   $40 %01000000 Stop     Stop
$05  5   $20 %00100000          <unknown>
$05  4   $10 %00010000          <unknown>
$05  3   $08 %00001000          <unknown>
$05  2   $04 %00000100          <unknown>
$05  1   $02 %00000010          <unknown>
$05  0   $01 %00000001 Count    Count/Doom
==== === === ========= ======== ===========================

==== === === ========= ======== ===========================
Byte Bit Hex Binary    In-Game  Description
==== === === ========= ======== ===========================
$06  7   $80 %10000000          <unknown>
$06  6   $40 %01000000          <unknown>
$06  5   $20 %00100000 Wall     Wall/Reflect
$06  4   $10 %00010000 Barrier  Barrier
$06  3   $08 %00001000          Image (two hits)
$06  2   $04 %00000100          Image (one hit)
$06  1   $02 %00000010          <unknown>
$06  0   $01 %00000001          HP Critical
==== === === ========= ======== ===========================

$07-$08: Current HP
^^^^^^^^^^^^^^^^^^^

These two bytes contain the character's current HP, encoded in low-endian
format. (In other words, the first byte is the low byte and the second byte is
the high byte.

$09-$0A: Maximum HP
^^^^^^^^^^^^^^^^^^^

These two bytes contain the chracter's maximum HP, encoded in low-endian
format.

$0B-$0C: Current MP
^^^^^^^^^^^^^^^^^^^

These two bytes contain the character's current MP, encoded in low-endian
format.

$0D-$0E: Maximum MP
^^^^^^^^^^^^^^^^^^^

These two bytes contain the character's maximum MP, encoded in low-endian
format.

$0F: Base Strength
^^^^^^^^^^^^^^^^^^

The character's base strength.

$10: Base Agility
^^^^^^^^^^^^^^^^^

The character's base agility.

$11: Base Vitality
^^^^^^^^^^^^^^^^^^

The character's base vitality.

$12: Base Wisdom
^^^^^^^^^^^^^^^^

The character's base wisdom.

$13: Base Will
^^^^^^^^^^^^^^

The character's base will.

$14: Strength
^^^^^^^^^^^^^

The character's base strength plus any bonuses from their equipment. If the
value is $B6 or greater (essentially -74 to -1, as this is signed), the value
is replaced with 1. This almost sets up a lower bound for the stat at 1, but 0
itself is allowed to pass through.

The upper bound is set to 99.

$15: Agility
^^^^^^^^^^^^

(calculated the same as strength)

$16: Vitality
^^^^^^^^^^^^^

(calculated the same as strength)

$17: Wisdom
^^^^^^^^^^^

(calculated the same as strength)

$18: Will
^^^^^^^^^

(calculated the same as strength)

$19: Elemental Attack
^^^^^^^^^^^^^^^^^^^^^

These are the elements associated with the character's physical attack. It is
set as the union of the attack elements of their weapons.

$1A: Racial Attack
^^^^^^^^^^^^^^^^^^

The character's physical attack will do extra damage against the races
indicated in this variable. It is determined by the union of the race property
of each of their weapons.

$1B: Physical Attack Multiplier
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is calculated with the following formula:

strength // 8 + agility // 16 + 1

$1C: Physical Attack Accuracy
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

There are a number of ways this might be calculated, depending on what the
character has equipped:

No Weapons
    50 + level // 4
One Weapon or Bow+Arrow
    (weapon or bow accuracy) + level // 4
Two Weapons
    (level // 4 + level // 4 + sum(weapon accuracies)) // 2

If only a bow or only an arrow is equipped, they are ignored.

This value is capped at 99.

$1D: Physical Attack Base
^^^^^^^^^^^^^^^^^^^^^^^^^

Like with accuracy, there are a few possibilities:

Yang (specifically class is Karate)
    level * 2 + strength // 4 + 2
Bow+Arrow
    bow_power // 2 + arrow_power + strength // 4
No Weapon
    level // 4 + strength // 4
One Weapon
    level // 4 + strength // 4 + weapon_power
Two Weapons
    (level // 4 + strength // 4) * 2 + sum(weapon powers)

If the player has a bow and arrow equipped and the bow is in the primary hand,
then the value is modified as follows:

value = value - (value // 5)

If only a bow or only an arrow is equipped, it is treated as a single weapon
with a power of 1.

This value is capped at 255.

$1E-$1F: Physical Attack Status
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This determines the status that physical attacks potentially carry. It is set
to the union of the status property of the character's weapons.

$20: Elemental Weakness
^^^^^^^^^^^^^^^^^^^^^^^

This byte controls the elements the character is weak to. This is set by taking
the opposite of the elements they are resistant to. The only pairs of opposite
elements are fire/ice and holy/darkness. Lightning and the pseudo-elements do
not have opposites.

For example, if the character resists fire and holy, they will have a weakness
to ice and darkness.

$21: Elemental Strong Weakness
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This byte determines which elements the character is strongly weak too. This
results in double the damage, versus a regular weakness. This value is set as
the opposite of any elemental immunities the character has.

This property is bugged. Once it is set, it will never be unset as the game has
no code to do so. Immunities and resistances take precedence over weaknesses in
general, so as long as that armor is equipped, there will be no problem.
However, once the character removes the equipment that gave the immunity, they
will retain a permanent strong weakness unless they re-equip the armor.

$22: Magical Defense Multiplier
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is calculated using the following formula:

(wisdom + will) // 32 + (agility // 32)

$23: Magical Defense Evasion
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is calculated using the following formula:

(wisdom + will) // 8 + sum(equipment magic evasion)

This is capped at 99.

$24: Magical Defense Base
^^^^^^^^^^^^^^^^^^^^^^^^^

This is calculated as the sum of the magical defense of the character's
equipment.

It is capped at 255.

$25: Elemental Resistance
^^^^^^^^^^^^^^^^^^^^^^^^^

The character resists these elements and takes reduced damage. This is
calculated by combining any resistances from all equipped equipment.

$26: Elemental Immunity
^^^^^^^^^^^^^^^^^^^^^^^

The character is completely immune to these elements. This is calculated by
combining any immunities from all equipped equipment.

$27: Race Resistance
^^^^^^^^^^^^^^^^^^^^

The character will take reduced damage from attacks by the races encoded in
this byte. Again, this is calculated by combining any race resistance
properties from the character's equipment.

$28: Physical Defense Multiplier
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is calculated according to the following formula:

:math:`\left \lfloor \frac{level}{16} \right \rfloor \cdot shields +
\left \lfloor \frac{agility}{8} \right \rfloor`

$29: Physical Defense Evasion
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This is set to the sum of the physical evade values of all the character's
equipment. An empty armor slot is considered to have an evade of 10. An empty
hand does not confer this bonus.

This value is capped at 99.

$2A: Physical Defense Base
^^^^^^^^^^^^^^^^^^^^^^^^^^

This is calculated as the sum of the physical defense of all the character's
equipment, plus half their vitality.

This value is capped at 255.

$2B-$2C: Status Immunity
^^^^^^^^^^^^^^^^^^^^^^^^

These two bytes determine which statuses the character is immune to. It is
determined by combining the status immunities of all the character's equipment.

$2D: Critical Rate
^^^^^^^^^^^^^^^^^^

This is the rate at which a character does critical hits. It is derived from
the character's base critical rate as follows:

No Weapon or Two Weapons
    Set to base_critical_rate
One Weapon
    base_critical_rate * 2
Bow+Arrow
    base_critical_rate * 3

This value is mostly capped at 99. Technically, in the single weapon case, it
is only capped if the base value is 128 or greater.

This value and the next value are an exception to the records at $2000 being a
strict superset of the records at $1000. In particular, these values in the
records at $1000 are always the base values. The derived values are only stored
in the in-battle records at $2000.

$2E: Critical Bonus
^^^^^^^^^^^^^^^^^^^

This is a bonus applied to critical hits. It is derived from the character's
base critical bonus as follows:

Bow+Arrow
    base_critical_bonus + arrow_power
One Weapon
    base_critical_bonus + weapon_power // 2
Two Weapons or No Weapon
    base_critical_bonus

This value is capped at 255.
