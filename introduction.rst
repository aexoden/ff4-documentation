Introduction
============

This project attempts to document the technical workings of Final Fantasy IV,
which was originally relased in the United States as Final Fantasy II. At this
time, the focus is on the US release Rev A (or 1.1). Eventually, however, the
goal is to document all SNES or SFC versions, and the differences among them.

Information is primary derived from an analysis of the ROM itself, mostly by
analyzing the code. Some well-known trivial information may be included as
well, so long as it is not contradicted by the code analysis. In general,
anecdotes, statistical sampling, or other published technical documentation are
not used as sources.

If you do find any errors, please contact `the author <jason@calindora.com>`_.
Please be prepared to back up any non-trivial corrections with either your own
code analysis or other compelling evidence.

Conventions
===========

Numbers
-------

Unless otherwise specified, all numbers are decimal. Hexadecimal numbers are
prefixed with a dollar sign: $. Binary numbers are prefixed with a percent
sign: %.

Bits within a byte (or double byte, etc.) are numbered starting from the least
significant bit, which is numbered zero. The highest bit in a byte, therefore,
is numbered 7. The remaining bits are numbered according to that pattern. The
correct value for a byte wherein a single bit is set can be determined by the
formula :math:`2^x` where :math:`x` is the bit number. (For example, :math:`2^0
= 1 = $01` and :math:`2^7 = 128 = $80`.
