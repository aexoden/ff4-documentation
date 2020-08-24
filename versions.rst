Versions
========

Over the course of development, several versions of the game were released. This
list currently only discusses the two USA releases, but there are additionally
at least two other Japanese releases (three if you count Easytype, which is
significantly different).

USA
---

This is the baseline USA release, often referred to as 1.0.

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

The motivation for this change is not clear. It does mean that if the player
puts zero of an item in their inventory via the item duplication glitch, the
value will be reset upon entering battle. However, it does not prevent the
glitch in the first place, nor does it present a significant barrier to using
it. In addition, the ID and count were already reset at the end of battle, so
so any important behavior change would be limited to something done in battle.

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
