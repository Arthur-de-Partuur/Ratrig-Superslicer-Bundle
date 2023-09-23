# Ratrig-Superslicer-Bundle
My SuperSlicer .ini file to be loaded in the Vendor folder

# Introduction
I modified the Ratrig.ini file that can be found in the %appdata%\SuperSlicer\vendor directory (go to "Help" and click "Show configuration folder" to easily locate this file), assuming you have previously installed the Ratrig printer from the configuration menu.

As I struggled with maintaining and aligning various parameters in between different print and printer profiles for my atrig VCore-3.1-300 3D-printer, I read an article on [Hackaday](https://hackaday.com/2022/10/18/3d-printering-managing-multiple-printing-profiles/) about SuperSlicer's Bundles . Althoug I do not fully grasp what this user is actually exporting, I started editing a copy of my Ratrig.ini file. Slowly I started to understand what the user is writing about.
# How to Download
**1)** Navigate to the .ini file.

**2)** Right click "Raw" and click "Save link as"

**If you do not use the "Raw" button, you will get errors trying to import.**

- ![](Images/Download.png) 

Alternatively, download the whole repository as .zip:

- ![](Images/DownloadRepo.png) 

- Or, if you have git installed, just `git clone https://github.com/AndrewEllis93/Ellis-SuperSlicer-Profiles.git` from your directory of choice.

# How it works
The general setup is such;
The file **can* contain 4 main categories that correspond with the tabs in SuperSlicer;
- [printer_model:MDL]
- [print:####]
- [printer:XYZ]
- [filament:ABC]
## Print - profiles
The beauty of this setup, is that you can create nested sections within each main category. So assuming you want 3 print profiles, each with a lot of generic stuff, but each its own layer height, you can create a common section (that you will need to put between two asterixes (*). This `*common*` file is **NOT** shown in SuperSlicer due to the asteriks. The first entry is the printer_model (speaks for it self). The second entry is then:
```
[print:*common*]
allow_empty_layers = 0
avoid_crossing_not_first_layer = 1
...
ALL YOUR "GENERIC" STUFF HERE
...
print_custom_variables = smooth_bed = false
...
...
layer_height = 0.15
...
wipe_tower_x = 170
wipe_tower_y = 140
```
```
[print:*smooth*]
print_custom_variables = smooth_bed = true
```
The next item is then specific or deviating from the `*common*` section. I do **not** put this between `*` as I **DO** want to show this in SuperSlicer. As I would need to have this profile contain everything mentioned in it as well as everything that is in `*common*`, it should start with *inherits*
```
[print:0.2mm NORMAL Smooth 0.4mm nozzle]
inherits = *common*; *smooth*
layer_height = 0.2
```
```
[print:0.2mm NORMAL Textured 0.4mm nozzle]
inherits = *common*;
layer_height = 0.2
```
As you can see, both `*common*` and `0.2mm NORMAL Smooth 0.4mm nozzle` contain the variable `layer_height`, however, as the profile starts with `*common*` and is then overruled by the new `layer_height` entry, the end result is that you have a print profile in SuperSlicer named `0.2mm NORMAL 0.4mm nozzle` that actualy has a layer_height of 0.2mm

The beauty of this system is that you only have to add below simple lines to create in total 3 print profiles with different layer height, whereby you only have to change the `*common*` section in one place if so required. You do not have to edit 3 profiles if you believe a certain setting, like for instance `avoid_crossing_perimeters_max_detour = 0`, should be changed in all profiles to `avoid_crossing_perimeters_max_detour = 1`). Subsequently, if you would need to have this veriable to be different for each print profile, you add this variable to the last, actual profile (the one that is not encised in `*`):
```
[print:0.3mm NORMAL Textured 0.4mm nozzle]
inherits = *common*
layer_height = 0.3
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter==0.40

[print:0.35mm NORMAL Smooth 0.4mm nozzle]
inherits = *common*; *smooth*
layer_height = 0.35
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter==0.40
```
Obviously, the layer height is just an example. You can go bonkers with it. I have created a `*common*` profile that is then referenced by profiles that have a varying nozzle diameter (as I run a E3D Revo HF) and made different profiles for use with a smooth PEI sheet bed and a powdercoated PEI sheet bed (these two have a differentZ-offset). Also, I have Highflow (HF) version and NORMAL versions

### Nozzle dependency
In my final print profile, I have added a `compatible_printers_condition` that looks for a **printer** profile with the proper nozzle diameter. This way, once you choose the **printer** in the printer tab with nozzle 0.4mm (profiles explained below), SuperSlicer only shows the **print** profiles that have this condition.

## Printer - profiles
Next on the list, I needed to make 4 printer-profiles with different extruder-diameters and could change between the smooth bed and textured (powder coated) bed.
All generic printer stuff is again placed in 
```
[printer:*common*]
start_gcode = START_PRINT EXTRUDER_TEMP=[first_layer_temperature] BED_TEMP=[first_layer_bed_temperature]
              {if smooth_bed == true}SET_GCODE_OFFSET Z=0.120     ;Smooth_bed Enabled
              {elsif smooth_bed == false}SET_GCODE_OFFSET Z=0     ;Smooth_bed Enabled\
              {endif}
```
Next, I made 4 invisible profiles that deal with the nozzle diameter:
```
[printer:*0.25nozzle*]
nozzle_diameter = 0.25
extruder_colour = #FFFF00
default_print_profile = 0.20mm Textured NORMAL 0.25nzl
```
```
[printer:*0.40nozzle*]
nozzle_diameter = 0.40
extruder_colour = #FF0000
default_print_profile = 0.20mm Textured NORMAL 0.4nzl
```
etc,
and I finaly combine this in:
```
[printer:RatRig Borg025]
inherits = *vcore-klipper*; *0.25nozzle*
```
```
[printer:RatRig Borg040]
inherits = *vcore-klipper*; *0.40nozzle*
```
etc.
### Smooth bed or Textured bed
As the `[print:*common*]` section contains a custom variable `smooth_bed` that is declared `false` (`print_custom_variables = smooth_bed = false`) and I have created a separate section `[print:*smooth*]` that sets this variable to true, I only have to add the `*smooth*` section to the inherits section of each print-profile to make a profile for the smooth PEI sheet bed (see below)

By adding some script in the printer `start gcode` to look for above variable, I can set the z-offset in the final gcode 
```
{if smooth_bed == true}SET_GCODE_OFFSET Z=0.120   ;Smooth_bed Enabled
{elsif smooth_bed == false}SET_GCODE_OFFSET Z=0     ;Smooth_bed Enabled\n{endif}
```
## Filament - profiles
This is still on the TODO list...
For now, I have just a bunch of .ini profiles that work fine

###Filament-dependant Pressure Advance
I have used a script in the filament Custom Gcode - Start GCode to look for the printer-nozzle size and adapt pressure advance to that:
```
; Filament gcode
; Set pressure advance per-filament for different nozzle sizes PETG @ 240 degr C & Nozzle Revo Voron / Orbiter 2.0
{if nozzle_diameter[0]==0.25}SET_PRESSURE_ADVANCE ADVANCE=0.0                    ;PA for 0.25nzl TODO
{elsif nozzle_diameter[0]==0.40}SET_PRESSURE_ADVANCE ADVANCE=0.089               ;PA for 0.40nzl
{elsif nozzle_diameter[0]==0.6}SET_PRESSURE_ADVANCE ADVANCE=0.045                ;PA for 0.6nzl 
{elsif nozzle_diameter[0]==0.8}SET_PRESSURE_ADVANCE ADVANCE=0.0                  ;PA for 0.8nzl TODO
{endif}
``` 
