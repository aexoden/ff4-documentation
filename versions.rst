Versions
========

Over the  course of development, several versions of the game were released. In
terms of apparent code progression (but not release date), the different
versions are:

1. Japan
2. Japan (Rev 1)
3. USA
4. USA (Rev 1)
5. Japan (Easytype)

As such, this document will discuss the changes between versions in this order.
Unless otherwise mentioned, assume a given version retains all changes made in
previous versions (for example, the first USA release is based on the second
Japanese release). The Japan (Easytype) release is something of a special case,
but it will be discussed in more detail later. This list is not necessarily
exhaustive, and some things may have been missed, primarily if they are changes
to data, rather than code.

Japan
-----

This is the originally released version of the game in Japan.

Japan (Rev 1)
-------------

This is the second and more common Japanese release.

Battle Scripts
~~~~~~~~~~~~~~

A bug was fixed regarding underworld battle scripts. Previously, the scripts on
the underworld map would be incorrect if the most recently visited dungeon map
was in a certain ID range.

USA
---

This is the baseline USA release, often referred to as 1.0. Several changes
were made from the Japanese releases. Some minor changes that were related to
the translation process may not be listed here.

Title Screen
~~~~~~~~~~~~

The title screen was changed, is no longer animated, and transparency effects
were removed.

Text
~~~~

Many changes were made to facilitate the translation of the game from Japanese
to English. This is not necessarily an exhaustive list.

Support for the diacritic tile row above text (used for voiced kana) was
largely replaced with a row of blank tiles instead. One of the instances of
this is at $00:B2A8, and leads to a rare bug involving a blank text box when
a fanfare is played by the dialog.

The legend display sequence saw minor coding changes.

Cycling between character sets in the Namingway screen was removed.

Support for dual-tile encoding was added. (The Japanese release had a very
limited DTE for the two-tile ellipsis.)

Display of the first character of item names (the icon) was added to the field
dialogs (including the item selection dialog).

The dialog opcodes handling the end of lines were changed slightly.

Battle Commands
~~~~~~~~~~~~~~~

Armor, Shell and Dispel are no longer added to Tellah at Mt.Ordeals.

Sylph
~~~~~

A bug with Sylph that caused it to incorrectly count the number of restored
targets by only ever checking the middle character was fixed.

Some code that superfluously updated the HP of the restored characters was
removed, but in the process, they accidentally added another bug that results
in the spell only costing MP if Rydia is in the middle slot.

Miscellaneous
~~~~~~~~~~~~~

The ability to customize joypad configuration was removed.

The limited multiplayer support was removed. (The Japanese release allowed
different characters to be controlled by different controllers in battle.)
Which character used which controller was fully configurable.

The active/wait ATB mode selection was removed and wait was made the default.
(However, the code to handle active mode was not removed.)

Outdoor trigger data was moved from bank $15 to bank $19.

When updating an inventory entry in battle (used at least when successfully
sneaking an item), code was removed that previously set the dartable bit of
the item.

The Warp color effect was changed slighty.

The statistics for various monsters were changed.

USA Rev 1
---------

This is the second and seemingly less common USA release, often referred to as
1.1. There were several minor changes made to the game:

Command Delay
~~~~~~~~~~~~~

The delay for the Kick command was changed from the character's relative speed
to half their relative speed.

The delay for the "Release" command (the finishing counterpart of Yang's Build
Up command) was changed from twice his relative speed to his relative speed.
Since the command is unavailable in the USA version, this change is effectively
pointless.

The delay for the finishing part of the Twin command was changed from the
relative speed to half the relative speed.

To summarize, Kick, Build Up, and Twin all had their respective delays halved.

Inventory
~~~~~~~~~

When entering battle, code was added to the routine at $03:8929 to reset the
item ID and count in both the field and battle inventories to zero if the item
count is zero.

The primary effect of this change is to prevent the player from being able to
duplicate consumables using the out-of-battle technique that uses the treasure
overflow screen. It does nothing, however, to prevent the more common method
used in battle to duplicate weapons.

Twin Mimic Glitch
~~~~~~~~~~~~~~~~~

Code was added to the routine at $03:A3ED (which processes the menu queue) to
reset the currently active slot to $FF if the current slot being processed is
flagged as the "other" twin. This effectively prevents the mimic glitch as
associated with Twin. Without this fix, during the next time through the loop,
that slot will be processed as if it has queued an action, which will repeat
the previous action.

This fix, however, does nothing to fix the Avenger mimic, which operates on a
similar principle.

Stop
~~~~

A line of code was removed from the routine at $03:D8EE (which handles the Stop
spell effect) that originally reset the target's action timer flags to zero. It
is currently unknown what the impact of this change is.

Explode
~~~~~~~

Code was added to the Explode routine at $03:DD12 to cap the damage at 9999,
even if the actor has more than 9999 HP.

Auto-Actions
~~~~~~~~~~~~

A single byte was changed in the routine at $03:AAF2 (which queues automatic
actions) to prevent the auto-actions from being queued if the slot has the
jumping status.

Address Offset Changes
~~~~~~~~~~~~~~~~~~~~~~

As a result of these changes, several offsets in the files changed. The offset
changes are all confined to bank $03. The table of corresponding ranges is
listed below (this table only documents changes that affect offsets--sections
with only byte-for-byte changes are listed as similar):

=================== =================== ============== =========
USA                 USA Rev 1           Classification Delta
=================== =================== ============== =========
$03:8000 - $03:8A85 $03:8000 - $03:8A85 Similar        0 ($00)
N/A                 $03:8A86 - $03:8A8F Added          N/A
$03:8A86 - $03:A4A7 $03:8A90 - $03:A4B1 Similar        10 ($0A)
$03:A4A8 - $03:A4A9 $03:A4B2 - $03:A4B9 Changed        N/A
$03:A4AA - $03:D906 $03:A4BA - $03:D916 Similar        16 ($10)
$03:D907 - $03:D909 N/A                 Removed        N/A
$03:D90A - $03:DD0E $03:D917 - $03:DD1B Similar        13 ($0D)
N/A                 $03:DD1C - $03:DD23 Added          N/A
$03:DD0F - $03:F26A $03:DD24 - $03:F27F Similar        21 ($15)
$03:F26B - $03:F27F N/A                 Removed ($FF)  N/A
$03:F280 - $03:FFFF $03:F280 - $03:FFFF Similar        0 ($00)
=================== =================== ============== =========

Japan (Easytype)
----------------

This was the final version of the game released in Japan. While it was
seemingly released before any of the USA versions, its codebase is clearly
further along than even USA (Rev 1). You can generally assume that it is the
same as USA (Rev 1), except regarding any text code (which is still based on
the latest Japanese release), or other changes listed below.

NOTE: Some of the below code changes have not been fully analyzed for their
implications in fixing bugs.

While the script and other text was based on the Japanese release, it was
modified and simplified, presumably to be more accessible to the intended
younger audience.

In the save/load menu, the check for the player pressing the B button occurs
earlier in the loop.

A wait for vblank call was removed from the Namingway character selection
screen.

Some dead code regarding custom joypad support was removed.

The item exchange screen added additional code to zero out the ID number of an
item in the spoils inventory to avoid leaving a glitched entry.

Additional code was added at the end of battle to copy hand inventory back to
the character records. The original version of the copying routine would zero
out the item ID if the count was zero. The Easytype version was modified to
instead zero out the count if the item ID was zero. This effectively fixes the
item duplication glitch by preventing the player from leaving the battle with
1 or 255 nothings (item ID 0) in their hand.
