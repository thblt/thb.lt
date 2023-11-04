---
title: Notes on KiCAD
cascade:
  type: docs
---

## PCB design

 - If you want to have a complex board shape, the easiest way is to
   design it in a different program and import it to the PCB.  DXF files
   are in absolute coordinates, so mind where you put the origin.
 - Footprints rotate around a reference point that is part of the
   footprint.  It may be painful to do precise rotation if the reference
   point is not at the center.  [Keebio
   footprints](https://github.com/keebio/Keebio-Parts.pretty) for MX
   switches/sockets are better than the built-in ones in that regard.
 - If you want to modify a footprint (eg for renaming pads!), do it in
   the schematic editor to make the DRC happy.  You'll get a DRC
   warning if you modify a footprint from the PCB editor.
 - Footprints are included in the document, you don't need to share
   libraries to share projects.  See sharing projects.

## Sharing projects

To share a kicad projects, you need:

 - `PROJECT.kicad_pro`, the project file itself.
 - `PROJECT.kicad_sch`, the schematic.
 - `PROJECT.kicad_pcb`, the PCB design.

If you have project-specific symbols/footprints, you can include:

 - `fp-lib-table`, the list of fingerprint libraries.
 - `sym-lib-table`, the list of symbol libraries.
 - Along with the libraries themselves.


You can delete or `.gitignore`:
```
# Kicad backups.  The actual name is PROJECT-backups
/*-backups

# Footprint cache
fp-info-cache

# Footprint and symbols libraries locations.
# You may want to keep this file if you bundle a project-specific
# library and you want to share it *as a library*. You don't need to
# share libraries just for the schematic and PCB: they include the
# actual symbols and footprints, not just references.

# Local config
*.kicad_prl

```
