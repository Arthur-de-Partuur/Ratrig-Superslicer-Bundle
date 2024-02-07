# Ratrig-Superslicer-Bundle
My SuperSlicer .ini file to be loaded in the Vendor folder

## Introduction
I modified the Ratrig.ini file that can be found in the %appdata%\SuperSlicer\vendor directory (go to "Help" and click "Show configuration folder" to easily locate this file), assuming you have previously installed the Ratrig printer from the configuration menu.

As I struggled with maintaining and aligning various parameters in between different print and printer profiles for my Ratrig VCore-3.1-300 3D-printer, I read an article on [Hackaday](https://hackaday.com/2022/10/18/3d-printering-managing-multiple-printing-profiles/) about SuperSlicer's Bundles . Althoug I do not fully grasp what this user is actually exporting, I started editing a copy of my Ratrig.ini file. Slowly I started to understand what the user is writing about.

## Why,.... for HEAVENS SAKE WHY!!!!
Yep,.... good question...

I was experiencing some issues with all the different parameters and how to align these between print profiles and filament profiles.
For instance, when I was tuning a PETG profile for Brand A, with a nozzle of 0.4mm I learned that the best result came with a temperature of 240 degrees C.
This is also true for all other profiles of that same brand but with another nozzle diameter. I found it troublesome to go back to all my other profiles and change it there as well. 

With below method, I now can change ``temperature = 240`` in ``[filament:*BRAND A PETG*]`` to have this updated in all my [PETG filament](https://github.com/Arthur-de-Partuur/Ratrig-Superslicer-Bundle/blob/main/README.md#filament) profiles of that brand.
The same goes for a LOT of parameters such as bridge_fan_speed, filament cooling parameters per brand or even per filament type

The most bennefit I get out of the print profiles. Speeds and Widths & Flow are regularly updated this way. The way Klipper works in combination with SuperSlicer makes it realy easy to use Custom variables that contain Pressure Advance values (in this case in the Notes section of the filament profile, but you can do a lot more with this):
![image](https://github.com/Arthur-de-Partuur/Ratrig-Superslicer-Bundle/assets/23432540/d284dbdd-eb93-4fa7-aecf-572f72d71b85)

These Values are used in the filament Custom G-code like so:

![image](https://github.com/Arthur-de-Partuur/Ratrig-Superslicer-Bundle/assets/23432540/5e71c9cb-2d9b-468f-9ac5-c210043ba497)

The way I set this up (see also further explanation below) is by creating a filament profile that contains the generic GCODE macro ``SET_PRESSURE_ADVANCE ADVANCE=[PA040]``, reading the Custom variable [PA040]:
```
[filament:PETG ColorFabb 0,40nzl]
inherits = *common*;*PETG-Common*;*PETG ColorFabb*
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA040]
```

The Variable PA040 is stored in one line in the ``filament:*PETG ColorFabb*`` profile
```
filament:\*PETG ColorFabb\*
filament_custom_variables = "PA025 = 0.160\nPA040 = 0.083\nPA060 = 0.042\nPA080 = 0.006"
```
So when I tune Pressure Advance, I update the outcome only in the INI file and I am assured it gets used everywhere.
## But Arthur, Can you not just store separate profiles?
Yes, that you can STILL do. With my ini file updated, I still make tweaks during itiration of a certain print. These updated profiles I save in SuperSlicer just like anyone would do. If I am happy with the print, I can update the ini file if needed and delete the modified profile from SuperSlicer, if I want.

## How to Download
**1)** Navigate to the .ini file.

**2)** Right click "Raw" and click "Save link as"

**If you do not use the "Raw" button, you will get errors trying to import.**

**Alternatively, download the whole repository as .zip**

## How to install
1. Go to your configuration folder. Easiest way is to open SuperSlicer and click Help / Show Configuration Folder:
![image](https://github.com/Arthur-de-Partuur/Ratrig-Superslicer-Bundle/assets/23432540/574e2a71-dbb2-44c9-afe4-944270b470a0)
1. Navigate to the "vendor" subfolder and copy paste the profile_test.ini -file in there (or the Ratrig file if you choose to copy my settings)
1. Navigate to the "profiles" subfolder and copy in the profile_test sub folder (if you use my ratrig.ini than you don't need to do this as there is already a ratrig folder. This profiles folder contains the pictures that can be used in the ini file to have your superslicer buildplate look "the part"
2. Now open or restart SuperSlicer and start the configuration wizard...
3. Look for profile_test (or ratrig if you choose my ini file) and sellect your printers with the right nozzle sizes
4. click next
5. at "Filaments" select your newly created Printer, Type, Vendor and Profile (make sure the boxes are ticked)
  ![image](https://github.com/Arthur-de-Partuur/Ratrig-Superslicer-Bundle/assets/23432540/03b2deb1-9fad-4e64-9c93-d1d4f15f4620)

7. Select Finish

# Build-up of the .ini file
## General
The .ini file contains 5 sections / chapters (whatever you want to call them)
The beginning of this file has some general stuff included so SuperSlicer knows what kind of printer it is dealing with, such as
- [vendor]
- [printer_model]

Further more, this file has "sections" for each Tab in SuperSlicer.
- [print]
- [filament]
- [printer]

Each corresponding "section" contains all the settings that you can work with to build your profile

# How to build up the file
All entries (or chapters) are stated within square brackets and contain the Tabname and the name of the entry: ``[tabname:entry name]``

**Note: There are NO spaces between the tabname and entry name, only a colon**

## STEP 1: Building blocks: Hidden entries
You can create hidden entries that will not be shown in SuperSlicer but that you can use to combine later when creating the final visible profile. You can create an entry for \"common PETG"\ that contains settings that you would like to always use when printing PETG (NOT brand specific for instance). Such a hidden entry is made by putting the entry name between **asterix' \*** like so:

**``[filament:*common PETG*]``**
**NOTE: Mind the Asterix at the beginning and end of the entry name!!!**

Items that you can include in the common part are for instance:
- bridge_fan_speed 
- chamber_temperature 
- disable_fan_first_layers
- fan_always_on 
- fan_below_layer_time 
- filament_type 
- max_fan_speed 
- max_speed_reduction 

The part in the ini file would look like so:
```
[filament:*common PETG*]
bridge_fan_speed = 40
chamber_temperature = 0
disable_fan_first_layers = 1
fan_always_on = 1
fan_below_layer_time = 10
filament_type = PETG
max_fan_speed = 30
max_speed_reduction = 90%
```

Another hidden entry that you could make is Brand specific info:

``[filament:*BRAND A PETG*]``

Items that you can include in the brand specific part are for instance:
- extrusion_multiplier
- filament_colour
- filament_cost
- filament_density 
- filament_diameter
- filament_shrink
- filament_soluble
- filament_spool_weight
- filament_vendor
- first_layer_bed_temperature
- first_layer_temperature
- temperature
- filament_max_speed

The part in the ini file would like like so:
```
[filament:*BRAND A PETG*]
extrusion_multiplier = 0.984
filament_colour = #000000
filament_cost = 27.5
filament_density = 1.28
filament_diameter = 1.7426
filament_shrink = 99.646%
filament_soluble = 0
filament_spool_weight = 1000
filament_vendor = BRAND-A
first_layer_bed_temperature = 80
first_layer_temperature = 250
temperature = 240
filament_max_speed = 290
```

## STEP 2: Profiles: visible entries
Now we need to create an entry that SuperSlicer is actualy going to show in the interface. If you want to create a filament profile for BRAND-A PETG, you would state
``[filament:BRAND-A PETG]`` **note: NO astrix \*\.**
- this would show up as:
![image](https://github.com/Arthur-de-Partuur/Ratrig-Superslicer-Bundle/assets/23432540/b4f6ff60-3452-4d00-a7f1-d8fd7ee18346)

**Note: I have not added any parameter in that entry and the picture only shows defaults at the moment.**

To now use the building blocks from above, the hidden entries, in the actual Profile, the visible entry, use the ``inherits`` parameter like so:
``inherits:*common PETG*; *BRAND A PETG*``

The entry would look something like this:
```
[filament:BRAND-A PETG]
inherits:*common PETG*; *BRAND A PETG*
compatible_printers_condition = printer_model=~/.*FWM-01.*/ && nozzle_diameter[0]==0.40
filament_max_volumetric_speed = 24
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA040]	;PA for 0.40nzl\n;SET_RETRACTION RETRACT_LENGTH=0.5 UNRETRACT_EXTRA_LENGTH=0.0"
```
Now, SuperSlicers combines this into one profile, like so:
![image](https://github.com/Arthur-de-Partuur/Ratrig-Superslicer-Bundle/assets/23432540/5a19afc3-1b6c-48b5-9b62-1749f4d588a8)

As you can see, I have added also a line for Max Volumetric flow, start_filament_gcode etc, etc.
Above is a simplefied version of what I use. 



# Usecase
To explain a bit more in detail how I believe the file works, see below explanations.

## [vendor]
Vendor name will be shown by the Config Wizard. In my example, I have used 
``name = PROFILE_TEST``

This is also the name of the INI file and I believe there should also be a folder with the same name to put bed_stl and png files in (not realy sure how this works yet)

## [printer_model:FWM-01]
Particularly the ``[printer model: ]`` can be handy in case you use more then one printer and want to make dependances between filaments and printers, for instance
see above also my inclusion for compatible_printers_condition = printer_model=~/.*FWM-01.*/
In this example, I have used:
```
name = Chris_McVie
variants = 0.40; 0.60
technology = FFF
family = FLEETWOODMAC
bed_model = rr-vc-300.stl
bed_texture = rr-vc-300.svg
```
In the configuration wizard, this will show as:
![image](https://github.com/Arthur-de-Partuur/Ratrig-Superslicer-Bundle/assets/23432540/3a605768-c311-47ee-a474-cb9f944dd586)

## [printer]
Although in SuperSlicer, the printer-tab is the last, Tab, I believe this should be the first tab (at least in my usecase). 

I use my printer with a variety of nozzle diameters (in this example 0.4mm and 0.6mm but in my actual profile I also use 0.25mm and 0.8mm) and multiple buildplates (Smooth PEI-sheet, Powdercoated PEI). I have calibrated my nozzle Z-offset on the most used plate (powdercoated PEI). I have learned that with the smooth PEI and POA I need a different Z-Offset. I have inserted these parameters in my printer profiles. 

In my workflow, I start in the Printer tab, choosing the right buildplate and nozzle size. In the example here, I would create a (hidden) ``[printer:*common*]`` section that houses all parameters you would always need, such as;
- machine limits (accel, feedrate)
- all specific GCODES for starting, interlayer and ending
- see the included profile_test.ini
![image](https://github.com/Arthur-de-Partuur/Ratrig-Superslicer-Bundle/assets/23432540/e92cb403-9f48-4f2b-b3b6-3f9235c0f232)


Next I create a printer specific section that defines one particulare printer (in case you would operate more printers of the same vendor and type, this could be handy. If not, this can also be combined with above common entry):
``[printer:*FWM-01-klipper*]`` (see included example to see what I have stuffed in here)

Next I want to make a separate entry ``[printer:*PEI-sheet*]`` for the differences in buildplate. The \*common* section is based on a z-offset with a powdercoated buildplate (= z-offset = 0). So the minimum I need to define here is the z-offset.
Same goes for the entries for the Nozzle sizes, ``[printer:*0.40nozzle*]`` and ``[printer:*0.60nozzle*]``. 

In general, below is now what we more or less have. We do NOT have any actual printer profiles as all printer entries are still hidden (due to the addition of the asterix' \*\, remember!!!)

```
[printer:*common*]
before_layer_gcode = ;BEFORE LAYER CHANGE\nTIMELAPSE_TAKE_FRAME
between_objects_gcode = 
color_change_gcode = M600
end_gcode = ;END PRINTER GCODE\nEND_PRINT\nSET_SKEW CLEAR=1\nM220 S100	;set printspeed back to 100% in case I have tweaked it during printing\nSET_GCODE_OFFSET Z=0	;Set Z-offset back to 0
gcode_flavor = klipper
layer_gcode = ;AFTER LAYER CHANGE GCODE\nSET_PRINT_STATS_INFO CURRENT_LAYER={layer_num + 1}\n;M118 Layer {layer_num+1}/[total_layer_count]: {filament_settings_id[0]}
machine_limits_usage = time_estimate_only
machine_max_acceleration_e = 20000
machine_max_acceleration_extruding = 8000
machine_max_acceleration_retracting = 9000
machine_max_acceleration_travel = 20000
machine_max_acceleration_x = 20000
machine_max_acceleration_y = 20000
machine_max_acceleration_z = 100

[printer:*FWM-01-klipper*]
bed_shape = 0x0,300x0,300x300,0x300
max_print_height = 300
printer_model = FWM-01
min_layer_height = 0.06
retract_lift_above = 0.2
use_firmware_retraction = 1
use_relative_e_distances = 1

[printer:*PEI-sheet*]
z-offset = 0.130
printer_notes = Profile for smooth PEI sheet / Sticker\n\n\n

[printer:*0.40nozzle*]
nozzle_diameter = 0.40
printer_variant = 0.40
extruder_colour = #FF0000

[printer:*0.60nozzle*]
nozzle_diameter = 0.60
printer_variant = 0.60
extruder_colour = #FF0000
```

Now, to create the actual printer profiles we start using the ``inherits`` function. The first actual printer profile will need to contain \*common\* (as should all profiles), the printer specific section and a nozzle size.
```
[printer:FWM-01 40nzl Powdercoated]
inherits = *common*; *FWM-01-klipper*; *0.40nozzle*
```
The next profile would be identicle accept for the buildplate / z-offset. I can do that in 2 ways;
- include all the hidden entries, one by one; ``inherits = *common*; *FWM-01-klipper*; *0.40nozzle*; *PEI-sheet*``
- inherits the previous profile and add the \*PEI-sheet\*; ``inherits = FWM-01 40nzl Powdercoated;*PEI-sheet*``

I have not tested the actual difference between the 2 methods but I suspect that the order of the mentioned entries in the ``inherits`` is important. In case 2 entries both have the same parameter but with a different value, the latest in line overrules the first one... but again, I have not checked this.

The next 2 entries will be more or less the same, but for the 0.60mm nozzle diameter
```
[printer:FWM-01 60nzl Powdercoated]
inherits = *common*; *FWM-01-klipper*; *0.60nozzle*

[printer:FWM-01 60nzl PEI-sheet]
inherits = FWM-01 60nzl Powdercoated;*PEI-sheet*
```
If we have done our job right, Superslicers printer profiles would show:
![image](https://github.com/Arthur-de-Partuur/Ratrig-Superslicer-Bundle/assets/23432540/0a1d2a05-daf1-40e7-890f-b17228c8c551)

## [filament]
Next (again, in my usecase) is the choice of filament. In here you can store all parameters relating to filament.
![image](https://github.com/Arthur-de-Partuur/Ratrig-Superslicer-Bundle/assets/23432540/d908a42e-1e1f-4042-b316-4828bdef8bca)


In line with above, you can first create hidden entries for:
- common ``[filament:*common*]``
- material specific but not Brand specific ``[filament:*common PETG*]``
- material + brand specific ``[filament:*BRAND A PETG*]``

After these hidden entries are done, you can start compiling the visible profiles and possibly make them dependent on nozzle diameter and add pressure advance parameters and flow parameters to them.
```
[filament:BRAND-A PETG 040]
compatible_printers_condition = printer_model=~/.*FWM-01.*/ && nozzle_diameter[0]==0.40
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA040]	;PA for 0.40nzl\n;SET_RETRACTION RETRACT_LENGTH=0.5 UNRETRACT_EXTRA_LENGTH=0.0"
extrusion_multiplier = 0.99
inherits = *common*;*common PETG*;*BRAND A PETG*
filament_max_volumetric_speed = 24

[filament:BRAND-B PETG 040]
compatible_printers_condition = printer_model=~/.*FWM-01.*/ && nozzle_diameter[0]==0.40
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA040]	;PA for 0.40nzl\n;SET_RETRACTION RETRACT_LENGTH=0.5 UNRETRACT_EXTRA_LENGTH=0.0"
extrusion_multiplier = 0.99
inherits = *common*;*common PETG*;*BRAND A PETG*
filament_max_volumetric_speed = 20

[filament:BRAND-A PETG 060]
compatible_printers_condition = printer_model=~/.*FWM-01.*/ && nozzle_diameter[0]==0.60
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA060]	;PA for 0.60nzl\n;SET_RETRACTION RETRACT_LENGTH=0.5 UNRETRACT_EXTRA_LENGTH=0.0"
extrusion_multiplier = 0.98
inherits = *common*;*common PETG*;*BRAND A PETG*
filament_max_volumetric_speed = 32

[filament:BRAND-B PETG 060]
compatible_printers_condition = printer_model=~/.*FWM-01.*/ && nozzle_diameter[0]==0.60
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA060]	;PA for 0.60nzl\n;SET_RETRACTION RETRACT_LENGTH=0.5 UNRETRACT_EXTRA_LENGTH=0.0"
extrusion_multiplier = 0.98
inherits = *common*;*common PETG*;*BRAND A PETG*
filament_max_volumetric_speed = 33
```

If all is well, we now have created some profiles that are dependent on nozzle size and printer model and that load specific Extrusion Multiplier Volumetric Speed and Pressure Advance settings.
So, if I choose the Printer FWM-01 with powder coated buildplate and nozzle 0.6
![image](https://github.com/Arthur-de-Partuur/Ratrig-Superslicer-Bundle/assets/23432540/fa135f53-5b79-46b4-8d7f-a0aa20013eb8)

I am only shown the filament profiles that are compiled and applicable for that printer (``Compatible_printers_condition = printer_model=~/.*FWM-01.*/ ``) and nozzle size `` && nozzle_diameter[0]==0.60``
![image](https://github.com/Arthur-de-Partuur/Ratrig-Superslicer-Bundle/assets/23432540/bc1fe53e-69f1-40ba-81d7-9d3843aeb554)

You can still see and use the other profiles by clicking on the green or red flag (SuperSlicer 2.5.59.6)
![image](https://github.com/Arthur-de-Partuur/Ratrig-Superslicer-Bundle/assets/23432540/3dc11aaf-6833-454d-858b-8c2484588650)

if this flag is not visible, you might want to enable this in the prefferences (Show incompatible print and filament presets):
![image](https://github.com/Arthur-de-Partuur/Ratrig-Superslicer-Bundle/assets/23432540/366bdf15-c884-4b08-9d03-b808a11f85ff)


## [print]
Here we store all the settings that are changing due to our printing preferences. The settings from the Print tab of SuperSlicer are in general:
![image](https://github.com/Arthur-de-Partuur/Ratrig-Superslicer-Bundle/assets/23432540/0d0d9320-301b-4c55-aa12-e71a3ac1f4e7)

They way you can use this is to make seperate entries for:
- different print speeds (high quality with lower speeds or flow rates or higher flowrates with possibly lower quality prints
- different layer heights for easy switching
- different profiles for different nozzle diameters

As before, start off with a \*common\* entry and fantasise from there:
```
[print:*common*]
all generic stuff here

[print:*0.20mm*]
;all print settings related to Layer height 0.20mm such as
layer_height = 0.20
bottom_solid_layers = 3

[print:*0.30mm*]
;all print settings related to Layer height 0.30mm
layer_height = 0.30
bottom_solid_layers = 2

[print:*0.40nzl*]
;all print settings related to nozzle size 0.40mm
compatible_printers_condition = printer_model=~/.*FWM-01.*/ && nozzle_diameter==0.40
external_perimeter_extrusion_width = 0.4
extrusion_width = 0.4
first_layer_extrusion_width = 0.4
infill_extrusion_width = 0.4
perimeter_extrusion_width = 0.4
solid_infill_extrusion_width = 0.4
top_infill_extrusion_width = 0.4

[print:*0.60nzl*]
;all print settings related to nozzle size 0.60mm
compatible_printers_condition = printer_model=~/.*FWM-01.*/ && nozzle_diameter==0.60
external_perimeter_extrusion_width = 0.6
extrusion_width = 0.6
first_layer_extrusion_width = 0.6
infill_extrusion_width = 0.6
perimeter_extrusion_width = 0.6
solid_infill_extrusion_width = 0.6
top_infill_extrusion_width = 0.6
```
And then to make up the actual profiles:
```
[print:0.20mm 0.40nzl]
inherits = *common*; *0.20mm*; *0.40nzl*

[print:0.30mm 0.40nzl]
inherits = *common*; *0.30mm*; *0.40nzl*

[print:0.20mm 0.60nzl]
inherits = *common*; *0.20mm*; *0.60nzl*

[print:0.30mm 0.60nzl]
inherits = *common*; *0.30mm*; *0.60nzl*
```
# The Actual RatRig.ini
My Ratrig.ini contains these sections;
![image](https://github.com/Arthur-de-Partuur/Ratrig-Superslicer-Bundle/assets/23432540/07ed95bf-d7e9-49c8-a76e-2c3d52590c37)



