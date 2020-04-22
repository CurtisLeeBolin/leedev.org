---
layout: post
title: 'Linux: Open-Source 3D Printing'
date: '2017-11-07 10:30:41'
---

Recently I built an [Anet A8](http://www.anet3d.com/English/3D_Printer/106.html) kit 3D printer.  I purchased it during a flash sale at [GearBest.com](https://www.gearbest.com/3d-printers-3d-printer-kits/pp_337314.html?wid=21) for less than $150 USD. I have seen it on short flash sales for less than $75 USD. This 3D printer kit is based on the [Prusa i3](http://reprap.org/wiki/Prusa_i3) from the open-source hardware and software [RepRap project](http://reprap.org/).  RepRap is short for replicating rapid prototyper.  The firmware is based on the open-source [Repetier Firmware](https://github.com/repetier/Repetier-Firmware) also from the RepRap project. The motherboard is based on the open-source hardware [Arduino](https://arduino.cc/) project.  This printer does not need to be connected to a computer to print.  It can print [G-code](http://reprap.org/wiki/G-code) directly from a micro SD card.  I plan to add the open-source hardware [Raspberry Pi](https://www.raspberrypi.org/) to the printer, and install the open-source software [OctoPrint](http://octoprint.org/) to remotely upload 3D objects, start, stop, and monitor the printer.  There is a huge repository of mostly openly licensed, [Creative Commons](https://creativecommons.org/), objects for you to download, modify, share, and print at [MakerBot Thiniverse](https://thingiverse.com/).  You might see a theme here; everything is or based on open-source projects/licenses.

## Anet A8
![Anet A8 01](/content/images/2017/11/Anet-A8-01-1.jpg)

## Desktop Software
All of the desktop software I use is open-source and runs on Linux, also MS Windows and Apple macOS.
*Arch Linux package name in parentheses ().*

### CAD Software for create the objects to print
* FreeCAD (freecad)
  * https://github.com/FreeCAD/FreeCAD
  * A general purpose 3D CAD modeler
* OpenSCAD (openscad)
   * https://github.com/openscad/openscad
   * The programmers solid 3D CAD modeller
### 3D Printer Software
* Ultimaker Cura (cura)
  * https://github.com/Ultimaker/Cura
  * Creates G-code (sliced object) for printer

## Ultimake Cura setup:
Select Prusa i3 as your printer

### Set the Correct Dimensions for the Anet A8
Preferences

* Printers
  * Prusa i3
    * Machine Settings
      * X (Width)
         * 220mm
      * Y (Depth)
         * 220mm
      * Z(Height)
         * 240mm