Introduction
============

This project attempts to document the technical workings of Final Fantasy IV,
which was originally relased in the United States as Final Fantasy II. At this
time, the focus is on the US release Rev A (or 1.1). Eventually, however, the
goal is to document all SNES or SFC versions, and the differences among them.

The information in this documentation comes primarily from two sources:

- Analysis of the disassembled code from the ROM itself.
- Observation of how things operate using a debugger.

Some well-known trivial information may be included as well to the extent that
it is not contradicted by either of the two previous sources. In contrast,
anecdotes, statistical sampling, or other published technical documentation are
not used as sources, though they may be used for confirmation or indications of
areas that merit further research.

Ultimately, I want this to be the most accurate resource regarding the inner
workings of Final Fantasy IV. Incomplete information is acceptable, but
incorrect information is not. If you do find any errors, please contact
`the author <jason@calindora.com>`_. Please be prepared to back up any
non-trivial corrections with either your own code analysis or other compelling
evidence.

Conventions
-----------

Numbers
^^^^^^^

Unless otherwise specified, all numbers are decimal. Hexadecimal numbers are
prefixed with a dollar sign: $. Binary numbers are prefixed with a percent
sign: %.

Bits within a byte (or double byte, etc.) are numbered starting from the least
significant bit, which is numbered zero. The highest bit in a byte, therefore,
is numbered 7. The remaining bits are numbered according to that pattern. The
correct value for a byte wherein a single bit is set can be determined by the
formula :math:`2^x` where :math:`x` is the bit number. (For example, :math:`2^0
= 1 = \$01` and :math:`2^7 = 128 = \$80`.

Addresses
^^^^^^^^^

All addresses are given as if they were being accessed on the SNES system bus.
Full 24-bit addresses are given as $BB:XXXX where BB is the bank and XXXX is
the address within that bank.

When accessing an actual ROM image outside of an SNES system, these addresses
should be converted according to the following formula:

``(bank * $10000) // 2 + (address - $8000)``

If for whatever reason, the ROM is headered, an additional $200 bytes should
be added for the header.

For example, $12:8200 corresponds to $090200 in an unheadered ROM image and
$13:EF00 corresponds to $09EF00.

Unless otherwise specified, all addresses refer to USA Rev 1. This is important
when discussing changes between versions, where offsets have shifted.
