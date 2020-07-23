ROM Map
=======

This chapter provides a detailed map of the ROM layout, organized by bank.

Bank $00
--------

.. csv-table::
    :header: "Address", "File Offset", "Type", "Name", "Description"
    :widths: 5, 5, 5, 10, 75

    "$00:8000.8301", "$000000.0301", "Code", "main", "First entry point for the game."

Bank $14
--------

.. csv-table::
    :header: "Address", "File Offset", "Type", "Name", "Description"
    :widths: 5, 5, 5, 10, 75

    "$14:8000.81FF", "$0A0000.0A01FF", "Data", "outdoor_tile_composition_data", "Tile composition data for the overworld."
