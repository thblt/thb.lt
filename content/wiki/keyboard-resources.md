---
title: Resources for keyboard makers
cascade:
  type: docs
---

## Software tools

 - The [keyboard layout
   editor](http://www.keyboard-layout-editor.com/) is an online tool
   to design (flat) keyboard layouts.
 - [KiCad](https://www.kicad.org/) is my electronics/PCB design tool
   of choice.
 - [Ergogen](https://github.com/ergogen/ergogen) generates (unrouted)
   PCBs, cases and plate designs, from a formal layout description.
 - FreeCAD and LibreCAD are two FLOSS CAD programs, focusing on 3D and
   2D CAD, respectively.
 - [Cosmos](https://ryanis.cool/cosmos-beta/) is a generator for
   Dactyl-style keyboards. (probably an evolution of the [Dactyl
   generator](https://ryanis.cool/dactyl/))

## Firware

 - [QMK](https://qmk.fm/) is the most common keyboard firmware project
   for *wired* keyboards.  Very easy to use, comes with a nice CLI and
   tons of features.
 - [ZMK](https://zmk.dev/) is another firmware, a bit less featurefull
   than QMK, but designed for *wireless* keyboards, including fully
   wireless splits.
 - [Keyberon](https://github.com/TeXitoi/keyberon) is a Rust `no_std`
   crate for writing keyboard firmwares, which I have yet to
   experiment.

## PCB

 - [Simple introduction to on-board MCUs](https://ohmbedded.com/keyboard-design-part-6-on-board-mcu-schematic-capture/)

## Other lists

 - A [very comprehensive list of
   tools](https://keyboard-design.com/tools.html).

## Manufacturers

 - LaserBoost (Barcelona, Spain) for laser cutting, including metal.
 - Sculpteo (France and others) for laser cutting (but not metal) and
   3D printing.
 - JLCPCB for PCBs.
 - PCBWay, also PCBs, a bit more expensive.

## Notes on T[R[R]]S cables

Which pins are shorted by plugs with less than four connectors.

Pins are, from tip to sleeve: T - R1 - R2 - S

 - In a TRS male connector, R2 == S (R2 is shorted with S)
 - In a TS male connector, R1 == R2 == S (R2 is shorted with S)

If you want maximum compatibility, if you need three wires, use T, R1 and S.  If you need two, use T and S.

```
╭─╮ ╭─╮ ╭─╮ T
│ │ │ │ │ │
┝━┥ ┝━┥ ┝━┥ R1
┝━┥ ┝━┥ │ │ R2
┝━┥ │ │ │ │ S
░▒▓ ░▒▓ ░▒▓
░▒▓ ░▒▓ ░▒▓
░▒▓ ░▒▓ ░▒▓
 ▀   ▀   ▀
```

## Hotswap sockets

### Millmax

Copy-pasted from [keep.io](https://keeb.io/products/mill-max-hotswap-sockets):

> - 7305-0: Gold-plated sockets (7305-0-15-15-47-27-10-0)
>   Shorter leg that can be more difficult to solder, but has a shorter lip above the PCB that's more flush
> - 0305-2: Tin-plated sockets (0305-2-15-80-47-80-10-0)
>   Easier to solder because of longer leg, but taller lip that pops up above the PCB
> - 3305-1: Gold-plated sockets (3305-1-15-15-47-27-10-0)
>   Best of both worlds! Easier to solder with the longer legs and is more flush with the top of the PCB with a lip than even more shorter than the 7305-0
