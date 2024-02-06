# Ratrig-Superslicer-Bundle
My SuperSlicer .ini file to be loaded in the Vendor folder

## Introduction
I modified the Ratrig.ini file that can be found in the %appdata%\SuperSlicer\vendor directory (go to "Help" and click "Show configuration folder" to easily locate this file), assuming you have previously installed the Ratrig printer from the configuration menu.

As I struggled with maintaining and aligning various parameters in between different print and printer profiles for my atrig VCore-3.1-300 3D-printer, I read an article on [Hackaday](https://hackaday.com/2022/10/18/3d-printering-managing-multiple-printing-profiles/) about SuperSlicer's Bundles . Althoug I do not fully grasp what this user is actually exporting, I started editing a copy of my Ratrig.ini file. Slowly I started to understand what the user is writing about.

## How to Download
**1)** Navigate to the .ini file.

**2)** Right click "Raw" and click "Save link as"

**If you do not use the "Raw" button, you will get errors trying to import.**

**Alternatively, download the whole repository as .zip**

# Build-up of the .ini file
## General
The beginning of this file has some general stuff included so SuperSlicer knows what kind of printer it is dealing with, such as
- [vendor]
- [printer_model:FWM-01]

Further more, this file has "Chapters" for each Tab in SuperSlicer.
- [print]
- [filament]
- [printer]

Each corresponding "chapter" contains all the settings that you can work with to build your profile

## How to build up the file
All entries (or chapters) are stated within square brackets and contain the Tabname and the name of the entry: "[Tabname:entry name]"

**Note: There are NO spaces between the tabname and entry name, only a colon**

### STEP 1: Hidden entries
You can create hidden entries that will not be shown in SuperSlicer but that you can use to combine later when creating the final visible profile. As per the above example of BRAND-A PETG, I can create an entry for \"common PETG"\ that contains settings that I would like to always use when printing PETG (NOT brand specific for instance). Such a hidden entry is made by putting the entry name between asterix' \*\, like so:
``[filament:*common PETG*]``

Items that you can include in the common part are for instance:
- bridge_fan_speed 
- chamber_temperature 
- disable_fan_first_layers
- fan_always_on 
- fan_below_layer_time 
- filament_type 
- max_fan_speed 
- max_speed_reduction 

The part in the ini file would like like so:
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

### STEP 2: visible entries
Now we need to create an entry that SuperSlicer is actualy going to show in the interface. If you want to create a filament profile for BRAND-A PETG, you would state
``[filament:BRAND-A PETG]`` **note: NO astrix \*\.**
- this would show up as:
![image](https://github.com/Arthur-de-Partuur/Ratrig-Superslicer-Bundle/assets/23432540/b4f6ff60-3452-4d00-a7f1-d8fd7ee18346)

**Note: I have not added any parameter in that entry and the picture only shows defaults at the moment.**

To now use the hidden entries in the visible entry, use the ``inherits`` parameter like so:
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

### [printer]
Although in SuperSlicer, the printer-tab is the last, Tab, I believe this should be the first tab (at least in my usecase). 

I use my printer with a variety of nozzle diameters (in this example 0.4mm and 0.6mm but in my actual profile I also use 0.25mm and 0.8mm) and multiple buildplates (Smooth PEI-sheet, Powdercoated PEI). I have calibrated my nozzle Z-offset on the most used plate (powdercoated PEI). I have learned that with the smooth PEI and POA I need a different Z-Offset. I have inserted these parameters in my printer profiles. 

In my workflow, I start in the Printer tab, choosing the right buildplate and nozzle size. In the example here, I would create a (hidden) ``[printer:*common*]`` section that houses all parameters you would always need, such as;
- machine limits (accel, feedrate)
- all specific GCODES for starting, interlayer and ending
- see the included profile_test.ini

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



## [print]
Here we store all the settings that are changing due to our printing preferences. The settings from the Print tab of SuperSlicer are in general:
![image](https://github.com/Arthur-de-Partuur/Ratrig-Superslicer-Bundle/assets/23432540/0d0d9320-301b-4c55-aa12-e71a3ac1f4e7)

They way you can use this is to make seperate entries for:
- different print speeds (high quality with lower speeds or flow rates or higher flowrates with possibly lower quality prints
- different layer heights for easy switching
- different profiles for different nozzle diameters

### Print \*Hidden*\

### Print Visible


### [filament]









[*******************************************************PRINT-COMMON-SECTION-BELOW*******************************************************]
# All presets starting with asterisk, for example *common*, are intermediate and they will
# not make it into the user interface.
#
# Common print preset
[print:*common*]
allow_empty_layers = 0
avoid_crossing_not_first_layer = 1
avoid_crossing_perimeters = 0
avoid_crossing_perimeters_max_detour = 0
bottom_fill_pattern = monotonic
bottom_solid_layers = 3
bottom_solid_min_thickness = 0
bridge_acceleration = 5000
bridge_angle = 0
bridge_fill_pattern = rectilinear
bridge_flow_ratio = 100%
bridge_internal_acceleration = 5000
bridge_overlap = 90%
bridge_overlap_min = 80%
bridge_speed = 25
bridge_speed_internal = 100%
bridge_type = nozzle
bridged_infill_margin = 200%
brim_acceleration = 5000
brim_ears = 0
brim_ears_detection_length = 1
brim_ears_max_angle = 160
brim_ears_pattern = concentric
brim_inside_holes = 0
brim_separation = 0.10
brim_speed = 50%
brim_width = 0
brim_width_interior = 0
clip_multipart_objects = 1
complete_objects = 0
complete_objects_one_brim = 0
complete_objects_one_skirt = 0
complete_objects_sort = object
default_acceleration = 6000
default_speed = 100
dont_support_bridges = 1
draft_shield = disabled
enforce_retract_first_layer = 1
ensure_vertical_shell_thickness = 0
exact_last_layer_height = 0
external_infill_margin = 150%
external_perimeter_acceleration = 5000
external_perimeter_cut_corners = 0%
external_perimeter_extrusion_spacing = 
external_perimeter_overlap = 100%
external_perimeter_speed = 200
external_perimeters_first = 0
external_perimeters_hole = 1
external_perimeters_nothole = 1
external_perimeters_vase = 0
extra_perimeters = 0
extra_perimeters_odd_layers = 0
extra_perimeters_overhangs = 0
extruder_clearance_height = 25
extruder_clearance_radius = 40
fill_angle = 45
fill_density = 25%
fill_pattern = cubic
fill_smooth_distribution = 10%
fill_smooth_width = 50%
fill_top_flow_ratio = 100%
first_layer_acceleration = 3000
first_layer_height = 70%
first_layer_infill_speed = 0
first_layer_min_speed = 0
first_layer_size_compensation = -0.20
first_layer_size_compensation_layers = 1
first_layer_speed = 80
gap_fill = 1
gap_fill_speed = 250
gcode_comments = 1
gcode_label_objects = 1
infill_acceleration = 5000
infill_anchor = 600%
infill_anchor_max = 5
infill_connection_top = notconnected
infill_every_layers = 1
infill_extruder = 1
infill_extrusion_spacing = 
infill_first = 0
infill_only_where_needed = 0
infill_overlap = 25%
infill_speed = 150
interface_shells = 0
ironing = 0
ironing_flowrate = 19%
ironing_spacing = 0.1
ironing_speed = 100
ironing_type = top
layer_height = 0.2
max_print_speed = 450
max_volumetric_speed = 25
min_skirt_length = 4
no_perimeter_unsupported_algo = bridges
;none, noperi, bridges, bridgesoverhangs, filled
notes = 
only_one_perimeter_top = 1
only_retract_when_crossing_perimeters = 1
output_filename_format = {input_filename_base}_lay={layer_height}mm_file={filament_settings_id[0]}_time={print_time}.gcode
over_bridge_flow_ratio = 100%
overhangs_acceleration = 2000
overhangs_width = 0
overhangs_width_speed = 0
perimeter_acceleration = 4000
perimeter_extruder = 1
perimeter_extrusion_spacing = 
perimeter_generator = classic
perimeter_speed = 200
perimeters = 3
raft_layers = 0
resolution = 0.0001
seam_angle_cost = 100%
seam_notch_inner = 0%
seam_notch_outer = 0%
seam_position = rear
seam_travel_cost = 100%
skirt_distance = 10
skirt_height = 1
skirts = 0
slice_closing_radius = 0.049
small_perimeter_speed = 25
solid_fill_pattern = rectilinear
solid_infill_acceleration = 0
solid_infill_below_area = 0
solid_infill_every_layers = 0
solid_infill_extruder = 1
solid_infill_extrusion_spacing =
solid_infill_extrusion_width = 125% 
solid_infill_speed = 200
solid_over_perimeters = 0
spacing =
spiral_vase = 0
;start_gcode = START_PRINT EXTRUDER_TEMP=[first_layer_temperature] BED_TEMP=[first_layer_bed_temperature] CHAMBER_TEMP={chamber_temperature} X0={first_layer_print_min[0]} Y0={first_layer_print_min[1]} X1={first_layer_print_max[0]} Y1={first_layer_print_max[1]}\n\nSET_PRINT_STATS_INFO TOTAL_LAYER=[total_layer_count]                      ;For additional Klipperscreen info\n\nSKEW_PROFILE LOAD=Tuurke_Skew                                               ;Load Skew Profile (https://www.klipper3d.org/Skew_Correction.html)\n\n; Set Z-Offset depending on print surface\nSET_GCODE_OFFSET Z=[bed_z_offset]
support_material = 1
support_material_acceleration = 0
support_material_angle = 0
support_material_auto = 0
support_material_bottom_contact_distance = 0.20
support_material_bottom_interface_layers = -1
support_material_buildplate_only = 1
support_material_contact_distance_bottom = 0.2
support_material_contact_distance_top = 0.2
support_material_extruder = 0
support_material_extrusion_width = 0
support_material_interface_acceleration = 0
support_material_interface_contact_loops = 0
support_material_interface_extruder = 0
support_material_interface_layers = 2
support_material_interface_pattern = rectilinear
support_material_interface_spacing = 0.2
support_material_interface_speed = 100%
support_material_pattern = rectilinear
support_material_solid_first_layer = 0
support_material_spacing = 2.5
support_material_speed = 0
support_material_synchronize_layers = 0
support_material_threshold = 65
support_material_with_sheath = 1
support_material_xy_spacing = 50%
thin_perimeters = 80%
thin_perimeters_all = 20%
thin_walls = 0
thin_walls_acceleration = 2000
thin_walls_speed = 100%
threads = 24
top_fill_pattern = rectilinear
top_infill_extrusion_width = 0.4
top_solid_infill_acceleration = 4000
top_solid_infill_speed = 200
top_solid_layers = 3							
top_solid_min_thickness = 0
travel_acceleration = 7000
travel_speed = 700
travel_speed_z = 50
wipe_tower = 0
wipe_tower_bridging = 10
wipe_tower_no_sparse_layers = 0
wipe_tower_rotation_angle = 0
wipe_tower_width = 60
wipe_tower_x = 170
wipe_tower_y = 140

[print:*HF*]
bridge acceleration = 0
bridge internal_acceleration = 0
brim_acceleration = 0	
default_acceleration = 7000
external_perimeter_acceleration = 6000
external_perimeter_speed = 250
first_layer_acceleration = 5000
infill_acceleration = 0
infill_speed = 280
;max_print_speed = 290
max_volumetric_speed = 40
overhanges_acceleraion = 0
perimeter_acceleration = 0
perimeter_speed = 0
solid_infill_acceleration = 0
solid_infill_speed = 280
support_material_interface_acceleration = 0
thin_walls_acceleration = 0
top_solid_infill_acceleration = 4500
travel_acceleration = 12000

[print:*SHF*]
bridge acceleration = 0
bridge internal_acceleration = 0
brim_acceleration = 0	
default_acceleration = 20000
first_layer_acceleration = 5000
infill_acceleration = 0
overhanges_acceleraion = 0
perimeter_acceleration = 0
solid_infill_acceleration = 0
support_material_interface_acceleration = 0
thin_walls_acceleration = 0
top_solid_infill_acceleration = 4500
travel_acceleration = 20000
[print:*0.12mm*]
bottom_solid_layers = 5
top_solid_layers = 5
layer_height = 0.12

[print:*0.20mm*]
layer_height = 0.20
bottom_solid_layers = 3

[print:*0.30mm*]
layer_height = 0.30
bottom_solid_layers = 2
top_solid_layers = 3
ensure_vertical_shell_thickness= 1

[print:*0.40mm*]
layer_height = 0.40
bottom_solid_layers = 2
top_solid_layers = 3
ensure_vertical_shell_thickness= 1

[print:*0.25nzl*]
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter==0.25
external_perimeter_extrusion_width = 0.25
extrusion_width = 
first_layer_extrusion_width = 0.3
infill_extrusion_width = 0
perimeter_extrusion_width = 0
solid_infill_extrusion_width = 0.25
top_infill_extrusion_width = 0.25
perimeters = 5

[print:*0.40nzl*]
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter==0.40
external_perimeter_extrusion_width = 0.0
extrusion_width = 
first_layer_extrusion_width = 0.4
infill_extrusion_width = 0
perimeter_extrusion_width = 0.0
solid_infill_extrusion_width = 0.4
top_infill_extrusion_width = 0.4
ironing_spacing = 0.2

[print:*0.60nzl*]
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter==0.60
external_perimeter_extrusion_width = 0
extrusion_width = 
first_layer_extrusion_width = 0.7
infill_extrusion_width = 0.8
perimeter_extrusion_width = 0
solid_infill_extrusion_width = 0.6
top_infill_extrusion_width = 0.6
perimeters = 2
first_layer_speed = 75
ironing_spacing = 0.3

[print:*0.80nzl*]
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter==0.80
external_perimeter_extrusion_width = 0
extrusion_width = 
first_layer_extrusion_width = 1.0
infill_extrusion_width = 0
perimeter_extrusion_width = 0
solid_infill_extrusion_width = 0.8
top_infill_extrusion_width = 0.8
perimeters = 2
first_layer_speed = 60

[*******************************************************PRINT-SPECIFIC-SECTION-BELOW*******************************************************]
# Below specific presets that will be viewable in SuperSicer interface
# Specific printprofiles for each nozzle diameter
# Specific printprofiles for each Layer height
#
[print:0.12mm NORMAL 0.25nzl]
inherits = *common*; *0.12mm*; *0.25nzl*

[print:0.12mm NORMAL 0.40nzl]	
inherits = *common*; *0.12mm*; *0.40nzl*


[print:0.20mm NORMAL 0.40nzl]
inherits = *common*; *0.20mm*; *0.40nzl*


[print:0.20mm HF 0.40nzl]
inherits = 0.20mm NORMAL 0.40nzl; *HF*


[print:0.20mm SHF 0.40nzl]
inherits = 0.20mm HF 0.40nzl; *SHF*


[print:0.12mm NORMAL 0.60nzl]
inherits = *common*; *0.12mm*; *0.60nzl*


[print:0.20mm NORMAL 0.60nzl]
inherits = *common*; *0.20mm*; *0.60nzl*

[print:0.20mm HF 0.60nzl]
inherits = 0.20mm NORMAL 0.60nzl; *HF*

[print:0.20mm SHF 0.60nzl]
inherits = 0.20mm HF 0.60nzl; *SHF*

[print:0.30mm NORMAL 0.60nzl]
inherits = *common*; *0.30mm*; *0.60nzl*


[print:0.30mm HF 0.60nzl]
inherits = 0.30mm NORMAL 0.60nzl; *HF*


[print:0.30mm SHF 0.60nzl]
inherits = 0.30mm HF 0.60nzl; *SHF*


[print:0.20mm NORMAL 0.80nzl]
inherits = *common*; *0.20mm*; *0.80nzl*


[print:0.30mm NORMAL 0.80nzl]
inherits = *common*; *0.30mm*; *0.80nzl*


[print:0.30mm HF 0.80nzl]
inherits = *common*; *0.30mm*; *0.80nzl*; *HF*


[print:0.40mm HF 0.80nzl]
inherits = *common*; *0.40mm*; *0.80nzl*; *HF*


[print:Eclipson (A) 0.20mm NORMAL 040nzl]
inherits = 0.20mm NORMAL 0.40nzl
bottom_solid_layers= 0
bridge_overlap = 50%
bridge_overlap_min = 50%
brim_acceleration = 0
brim_ears_max_angle = 125
brim_separation = 0.1
brim_width = 3
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter==0.40
ensure_vertical_shell_thickness = 0
external_perimeter_extrusion_width = 0.0
external_perimeter_speed = 175
extrusion_spacing = 
extrusion_width = 0
fill_density = 0%
first_layer_extrusion_width = 0
first_layer_size_compensation = 0
first_layer_speed = 50
gap_fill= 1
gap_fill_acceleration= 2000
gap_fill_min_area= 50%
gap_fill_speed= 50
infill_extrusion_width = 160%
infill_speed= 0
max_volumetric_speed= 36
min_skirt_length= 0
only_one_perimeter_top= 0
perimeter_extrusion_width= 0
perimeter_speed= 300
perimeters = 1
skirts = 5
slice_closing_radius = 0
small_perimeter_speed = 25%
solid_infill_extrusion_width = 140%
solid_infill_speed = 0
support_material_extrusion_width = 0
support_material_interface_speed = 100
support_material_speed = 120
support_material_threshold = 30
support_material_with_sheath = 0
thin_walls = 1
thin_walls_min_width = 100%
top_fill_pattern = monotonicgapfill
top_infill_extrusion_width = 100%
top_solid_infill_acceleration = 0
top_solid_layers = 0

[print:Eclipson (A-LW) 0.20mm NORMAL 040nzl]
inherits= Eclipson (A) 0.20mm NORMAL 040nzl
bottom_solid_layers = 2
bridge_brim_separation = 0.55
bridge_flow_ratio = 85%
brim_width = 0
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter==0.40
default_acceleration = 5000
ensure_vertical_shell_thickness = 1
gap_fill_enabled = 0
min_skirt_length = 10
over_bridge_flow_ratio = 105%
overhangs_reverse = 1
perimeter_extrusion_spacing = 
perimeter_extrusion_width = 0
seam_travel_cost = 20%
skirts = 2
thin_walls = 0
thin_walls_min_width = 33%
top_solid_layers = 2

[print:Eclipson (C) 0.12mm NORMAL 040nzl]
inherits= 0.12mm NORMAL 0.40nzl
bottom_solid_layers= 4
bridge_flow_ratio= 85%
bridge_infill_margin = 300%
bridge_overlap = 75%
bridge_overlap_min = 50%
brim_acceleration = 0
brim_ears_max_angle = 125
brim_separation = 0.25
default_acceleration= 6800
enforce_retract_first_layer = 0
ensure_vertical_shell_thickness= 0
external_perimeter_extrusion_spacing = 
external_perimeter_extrusion_width = 0
external_perimeter_speed = 250
extrusion_spacing = 
extrusion_width = 0
first_layer_speed = 60
gap_fill_acceleration = 2000
gap_fill_min_area = 50%
gap_fill_speed = 150
infill_extrusion_width = 160%
infill_speed = 300
max_volumetric_speed = 36
min_skirt_length = 0
only_one_perimeter_top = 0
only_retract_when_crossing_perimeters = 1
over_bridge_flow_ratio= 110%
perimeter_acceleration = 3000
perimeter_extrusion_spacing = 
perimeter_speed = 300
perimeters = 4
small_perimeter_speed = 25%
solid_infill_extrusion_width = 140%
solid_infill_speed = 0
support_material_extrusion_width = 0
support_material_interface_speed = 100
thin_walls= 1
top_fill_pattern = monotonicgapfill
top_infill_extrusion_width = 100%
top_solid_infill_acceleration = 0
top_solid_layers= 6

[*******************************************************PRINTER-COMMON-SECTION-BELOW*******************************************************]
# All presets starting with asterisk, for example *common*, are intermediate and they will
# not make it into the user interface.
#
# Common printer preset
#
[printer:*vcore-klipper*]
inherits = *common*
bed_shape = 0x0,300x0,300x300,0x300
max_print_height = 300
printer_model = VC3_300
min_layer_height = 0.06
retract_lift_above = 0.2
use_firmware_retraction = 1
use_relative_e_distances = 1
use_volumetric_e = 0
arc_fitting = 0
wipe_only_crossing_perimeters = 0
fan_speedup_time = 1.0
fan_kickstart = 0.5
z_step = 0.02
fan_printer_min_speed = 14

[printer:*common*]
before_layer_gcode = ;BEFORE LAYER CHANGE\nTIMELAPSE_TAKE_FRAME
between_objects_gcode = 
color_change_gcode = M600
cooling_tube_length = 5
cooling_tube_retraction = 91.5
deretract_speed = 40
end_gcode = ;END PRINTER GCODE\nEND_PRINT\nSET_SKEW CLEAR=1\nM220 S100	;set printspeed back to 100% in case I have tweaked it during printing\nSET_GCODE_OFFSET Z=0	;Set Z-offset back to 0
extra_loading_move = -2
feature_gcode = ; once we start printing infill, increase square corner velocity to 10\n{if extrusion_role=="InternalInfill"}SET_VELOCITY_LIMIT SQUARE_CORNER_VELOCITY=10 {endif}\n; when we switch to other features, go back to 5\n{if extrusion_role!="InternalInfill"}SET_VELOCITY_LIMIT SQUARE_CORNER_VELOCITY=5 {endif}
gcode_flavor = klipper
high_current_on_filament_swap = 0
layer_gcode = ;AFTER LAYER CHANGE GCODE\nSET_PRINT_STATS_INFO CURRENT_LAYER={layer_num + 1}\n;M118 Layer {layer_num+1}/[total_layer_count]: {filament_settings_id[0]}
machine_limits_usage = time_estimate_only
machine_max_acceleration_e = 20000
machine_max_acceleration_extruding = 8000
machine_max_acceleration_retracting = 9000
machine_max_acceleration_travel = 20000
machine_max_acceleration_x = 20000
machine_max_acceleration_y = 20000
machine_max_acceleration_z = 100
machine_max_feedrate_e = 60
machine_max_feedrate_x = 450
machine_max_feedrate_y = 450
machine_max_feedrate_z = 70
machine_max_jerk_e = 5
machine_max_jerk_x = 5
machine_max_jerk_y = 5
machine_max_jerk_z = 0.4
machine_min_extruding_rate = 0
machine_min_travel_rate = 0
max_layer_height = 75%
parking_pos_retraction = 92
pause_print_gcode = M601
printer_custom_variables = bed_z_offset = 0
printer_notes = Don't remove the following keywords! These keywords are used in the "compatible printer" condition of the print and filament profiles to link the particular print and filament profiles to this printer profile.\nPRINTER_VENDOR_RatRig\nPRINTER_MODEL_VCORE\nPRINTER_HAS_Directextruder\n\n\n
remaining_times = 1
;retract_before_travel = 1
;retract_before_wipe = 0%
;retract_layer_change = 1
;retract_length = 0
;retract_length_toolchange = 0
;retract_lift = 0
;retract_lift_below = 0
;retract_restart_extra = 0
;retract_restart_extra_toolchange = 0
silent_mode = 0
single_extruder_multi_material = 0
start_gcode = ;START PRINTER GCODE\nSTART_PRINT EXTRUDER_TEMP=[first_layer_temperature] BED_TEMP=[first_layer_bed_temperature] X0={first_layer_print_min[0]} Y0={first_layer_print_min[1]} X1={first_layer_print_max[0]} Y1={first_layer_print_max[1]}\n\n;For additional Klipperscreen info\nSET_PRINT_STATS_INFO TOTAL_LAYER=[total_layer_count]\n\n;Load Skew Profile (https://www.klipper3d.org/Skew_Correction.html)\nSKEW_PROFILE LOAD=Tuurke_Skew	\n\n;Set Z-Offset depending on print surface\n{if bed_z_offset>0} SET_GCODE_OFFSET Z=[bed_z_offset] {endif}\n\n;Echo the main Print, Printer & Filament ID's\nRESPOND TYPE=echo MSG="Printer settings: {printer_settings_id}"\nRESPOND TYPE=echo MSG="Print settings: {print_settings_id}"\nRESPOND TYPE=echo MSG="filament settings: {filament_settings_id[0]}"\n
start_gcode_manual = 1
thumbnails = 32x32,300x300
thumbnails_end_file = 1
toolchange_gcode = 
variable_layer_height = 0

[printer:*PEI-sheet*]
printer_custom_variables = bed_z_offset = 0.130
printer_notes = Profile for smooth PEI sheet / Sticker\n\n\n

[printer:*POA-sheet*]
printer_custom_variables = bed_z_offset = 0.100
printer_notes = Profile for smooth POA sheet\n\n\n

[printer:*0.25nozzle*]
nozzle_diameter = 0.25
printer_variant = 0.25
extruder_colour = #FFFF00
default_print_profile = 0.13mm NORMAL 0.25nzl

[printer:*0.40nozzle*]
nozzle_diameter = 0.40
printer_variant = 0.40
extruder_colour = #FF0000
default_print_profile = 0.20mm HF 0.4nzl

[printer:*0.60nozzle*]
nozzle_diameter = 0.60 
printer_variant = 0.60
extruder_colour = #0000FF
default_print_profile = 0.30mm HF 0.6nzl

[printer:*0.80nozzle*]
nozzle_diameter = 0.80
printer_variant = 0.80
extruder_colour = #00FF00
default_print_profile = 0.30mm HF 0.8nzl


[*******************************************************PRINTER-SPECIFIC-SECTION-BELOW*******************************************************]
# Below specific presets that will be viewable in SuperSicer interface
# Section are for each Nozzle Size
# "PEI-sheet"indicates a flat PEI sheet/sticker. If omitted, it uses powder-coated bed. Difference in in Z-Offset
#
[printer:RatRig VC3_300 025nzl Powdercoated]
inherits = *vcore-klipper*; *0.25nozzle*
default_materials = PETG Jupiter 0,25nzl


[printer:RatRig VC3_300 025nzl PEI-sheet ]
inherits = RatRig VC3_300 025nzl Powdercoated;*PEI-sheet*


[printer:RatRig VC3_300 025nzl POA-sheet ]
inherits = RatRig VC3_300 025nzl Powdercoated;*POA-sheet*


[printer:RatRig VC3_300 040nzl Powdercoated]
inherits = *vcore-klipper*; *0.40nozzle*
default_materials = PETG Jupiter 0,40nzl


[printer:RatRig VC3_300 040nzl PEI-sheet]
inherits = RatRig VC3_300 040nzl Powdercoated;*PEI-sheet*
printer_settings_id = "PEI_0.40nzl"

[printer:RatRig VC3_300 040nzl POA-sheet]
inherits = RatRig VC3_300 040nzl Powdercoated;*POA-sheet*


[printer:RatRig VC3_300 060nzl Powdercoated]
inherits = *vcore-klipper*;*0.60nozzle*


[printer:RatRig VC3_300 060nzl PEI-sheet]
inherits = RatRig VC3_300 060nzl Powdercoated;*PEI-sheet*

[printer:RatRig VC3_300 060nzl POA-sheet]
inherits = RatRig VC3_300 060nzl Powdercoated;*POA-sheet*

[printer:RatRig VC3_300 080nzl Powdercoated]
inherits = *vcore-klipper*;*0.80nozzle*
default_materials = PETG Jupiter 0,80nzl

[printer:RatRig VC3_300 080nzl PEI-sheet]
inherits = RatRig VC3_300 080nzl Powdercoated;*PEI-sheet*


[printer:RatRig VC3_300 080nzl POA-sheet]
inherits = RatRig VC3_300 080nzl Powdercoated;*POA-sheet*


[*******************************************************FILAMENT-COMMON-SECTION-BELOW*******************************************************]
# All presets starting with asterisk, for example *common*, are intermediate and they will
# not make it into the user interface.
#
# Common printer preset
[filament:*common*]
filament_max_overlap = 100%
support_material_interface_fan_speed = -1
top_fan_speed = -1


[filament:*PETG-Common*]
bed_temperature = 80
bridge_fan_speed = 40
chamber_temperature = 0
disable_fan_first_layers = 1
external_perimeter_fan_speed = 30
fan_always_on = 1
fan_below_layer_time = 10
filament_custom_variables = "PA025 = 0.120\nPA040 = 0.06\nPA060 = 0.04\nPA080 = 0.006"
filament_notes = "'Safe values' for Pressure Advance (PA).\nFilament needs to be calibrated still"
filament_type = PETG
max_fan_speed = 30
max_speed_reduction = 90%
default_fan_speed = 25
min_print_speed = 20
seam_gap = 0
slowdown_below_layer_time = 5

[filament:*LW-PLA-Common*]
bridge_fan_speed = 35
disable_fan_first_layers = 2
external_perimeter_fan_speed = -1
fan_always_on = 1
fan_below_layer_time = 5
filament_max_overlap = 100%
;filament_notes = "Nozzle 0.4mm, Max volumatric flowrate with 240C ~24mm3/s\nNozzle 0.6mm, Max volumatric flowrate with 240C ~40mm3/s\nSet max print speed to 60 as this is determing the quality of LW-PLA"
filament_skinnydip_distance = 31
filament_soluble = 0
filament_type = PLA
full_fan_speed_layer = 4
max_fan_speed = 30
max_speed_reduction = 90%
default_fan_speed = 40
min_print_speed = 10
slowdown_below_layer_time = 2
;start_filament_gcode = "; Filament gcode\n; Set pressure advance per-filament for different nozzle sizes 19-04-2023 Colorfab LW-PLA @ 235 degr C & Nozzle 0.4mm Rapido UHF / Orbiter 2.0\n{if nozzle_diameter[0]==0.4}SET_PRESSURE_ADVANCE ADVANCE=0.82\n{elsif nozzle_diameter[0]==0.6}SET_PRESSURE_ADVANCE ADVANCE=0.82\n{endif}\n\nSET_RETRACTION RETRACT_LENGTH=0.0 UNRETRACT_EXTRA_LENGTH=0\n\n;RESPOND MSG=\"Setting Z-Offset to -0.18 to enable LW-PLA Bed adhesion\"\n;SET_GCODE_OFFSET Z=-0.18          ;bring toolhead closer to bed as first laayer adhesion with LW-PLA sucks. Combined with 0.3mm first layer heigth"
support_material_interface_fan_speed = -1
top_fan_speed = -1

[filament:*ASA-Common*]
bed_temperature = 110
bridge_fan_speed = 25
brim_width = 5
chamber_temperature = 35
default_fan_speed = 15
disable_fan_first_layers = 10
external_perimeter_fan_speed = 25
fan_always_on = 0
fan_below_layer_time = 10
filament_custom_variables = "PA025 = 0.0\nPA040 = 0.021\nPA060 = 0.024\nPA080 = 0.006"
filament_type = ASA
max_fan_speed = 20
max_speed_reduction = 90%
min_print_speed = 10
perimeter_fan_speed = 25
slowdown_below_layer_time = 8

[filament:*TPU-Common*]
bed_temperature = 40
bridge_fan_speed = 30
disable_fan_first_layers = 2
fan_always_on = 0
fan_below_layer_time = 5
filament_cooling_final_speed = 3.4
filament_cooling_initial_speed = 2.2
filament_cooling_moves = 4
filament_cooling_zone_pause = 0
filament_type = FLEX
first_layer_bed_temperature = 40
first_layer_temperature = 220
full_fan_speed_layer = 4
max_fan_speed = 50
max_speed_reduction = 95%
default_fan_speed = 35
min_print_speed = 10
slowdown_below_layer_time = 0
top_fan_speed = -1


[filament:*PETG Jupiter*]
extrusion_multiplier = 1
filament_colour = #000000
filament_cost = 27.5
filament_custom_variables = "PA025 = 0.160\nPA040 = 0.083\nPA060 = 0.042\nPA080 = 0.006"
filament_density = 1.28
filament_diameter = 1.7426
filament_notes = "Calibration of PA and VF on 27-01-2024"
filament_shrink = 99.646%
filament_soluble = 0
filament_spool_weight = 1000
filament_vendor = 123-3D
first_layer_bed_temperature = 80
first_layer_temperature = 250
temperature = 240
filament_max_speed = 290

[filament:*PETG ColorFabb*]
extrusion_multiplier = 0.955
filament_colour = #000000
filament_cost = 36.66
filament_diameter = 1.7345
;filament_notes = "Calibration of PA and VF on 02-10-2023"
;filament_custom_variables = "PA025 = 0.0\nPA040 = 0.0\nPA060 = 0.0\nPA080 = 0.0"
filament_density = 1.28
filament_shrink = 99.8%
filament_soluble = 0
filament_spool_weight = 750
filament_vendor = ColorFabb
first_layer_bed_temperature = 80
first_layer_temperature = 250
temperature = 240

[filament:*PETG REAL*]
extrusion_multiplier = 0.9408
filament_colour = #FF8000
filament_cost = 19.90
filament_custom_variables = "PA025 = 0.125\nPA040 = 0.0425\nPA060 = 0.037\nPA080 = 0.006"
filament_density = 1.27
filament_diameter = 1.722
filament_notes = "Calibration of PA and VF on 02-10-2023"
;filament_shrink = 
filament_soluble = 0
filament_spool_weight = 500
filament_vendor = REAL
first_layer_bed_temperature = 80
first_layer_temperature = 250
temperature = 240

[filament:*PETG eSun*]
extrusion_multiplier = 0.98
filament_colour = #808080
filament_cost = 21.84
filament_custom_variables = "PA025 = 0.175\nPA040 = 0.075\nPA060 = 0.04\nPA080 = 0.0005"
filament_density = 1.27
filament_diameter = 1.737
filament_notes = "Calibration of PA and VF on 06-10-2023"
;filament_shrink = 
filament_soluble = 0
filament_spool_weight = 1000
filament_vendor = eSun
first_layer_bed_temperature = 80
first_layer_temperature = 250
temperature = 240

[filament:*LW-PLA ColorFabb*]
bed_temperature = 70
chamber_temperature = 0
extrusion_multiplier = 0.53
filament_colour = #000000
filament_cost = 27.5
filament_custom_variables = "PA025 = 0.82\nPA040 = 0.82\nPA060 = 0.82\nPA080 = 0.006"
filament_density = 1.28
filament_deretract_speed = nil
filament_diameter = 1.75
filament_max_speed = 140
filament_shrink = 100%
filament_spool_weight = 750
filament_vendor = ColorFabb
first_layer_bed_temperature = 80
first_layer_temperature = 235
temperature = 225

[filament:*ASA ColorFabb*]
extrusion_multiplier = 0.945
filament_colour = #000000
filament_cost = 45.31
filament_custom_variables = "PA025 = 0.075\nPA040 = 0.021\nPA060 = 0.035\nPA080 = 0.006"
filament_density = 1.07
filament_diameter = 1.75
filament_notes =
filament_shrink = 99%
filament_soluble = 0
filament_spool_weight = 650
filament_vendor = ColorFabb
first_layer_bed_temperature = 110
first_layer_temperature = 275
temperature = 265
support_material_interface_fan_speed = 50

[filament:*TPU AzureFilm*]
extrusion_multiplier = 0.89
filament_cost = 20
filament_custom_variables = "PA025 = 0.0\nPA040 = 0.021\nPA060 = 0.024\nPA080 = 0.006" ;NOT CALIBRATED
filament_density = 1.24
filament_diameter = 1.75
filament_dip_extraction_speed = 70
filament_dip_insertion_speed = 33
filament_enable_toolchange_part_fan = 0
filament_enable_toolchange_temp = 0
filament_load_time = 0
filament_loading_speed = 28
filament_loading_speed_start = 3
filament_max_overlap = 100%
filament_max_speed = 50
filament_max_volumetric_speed = 10
filament_max_wipe_tower_speed = 0
filament_melt_zone_pause = 0
filament_minimal_purge_on_wipe_tower = 15
filament_retract_lift = 0.1
filament_shrink = 100%
filament_skinnydip_distance = 31
filament_soluble = 0
filament_unload_time = 0
filament_unloading_speed = 90
filament_unloading_speed_start = 100
filament_use_fast_skinnydip = 0
filament_use_skinnydip = 0
filament_vendor = AzureFilm
temperature = 220

[*******************************************************FILAMENT-SPECIFIC-SECTION-BELOW*******************************************************]
# Below specific presets that will be viewable in SuperSicer interface
# Sections are for each filament type,
# Nozzle Size and filament brand specific
# 
[filament:PETG Jupiter 0,25nzl]
inherits = *common*;*PETG-Common*;*PETG Jupiter*
filament_max_volumetric_speed = 14
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.25
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA025]   ;PA for 0.25nzl\n;SET_RETRACTION RETRACT_LENGTH=0.5 UNRETRACT_EXTRA_LENGTH=0.0"
filament_settings_id = "PETG_JUP_025"

[filament:PETG Jupiter 0,40nzl]
inherits = *common*;*PETG-Common*;*PETG Jupiter*
filament_max_volumetric_speed = 24
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.40
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA040]	;PA for 0.40nzl\n;SET_RETRACTION RETRACT_LENGTH=0.5 UNRETRACT_EXTRA_LENGTH=0.0"
filament_settings_id = "PETG_JUP_040"
extrusion_multiplier = 0.99

[filament:PETG Jupiter 0,60nzl]
inherits = *common*;*PETG-Common*;*PETG Jupiter*
filament_max_volumetric_speed = 33
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.60
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA060]	;PA for 0.60nzl\nSET_RETRACTION RETRACT_LENGTH=0.5 UNRETRACT_EXTRA_LENGTH=0.0"
filament_settings_id = "PETG_JUP_060"
extrusion_multiplier = 0.99

[filament:PETG Jupiter 0,80nzl]
inherits = *common*;*PETG-Common*;*PETG Jupiter*
filament_max_volumetric_speed = 33
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.80
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA080]	;PA for 0.80nzl\n;SET_RETRACTION RETRACT_LENGTH=0.5 UNRETRACT_EXTRA_LENGTH=0.0"
filament_settings_id = "PETG_JUP_080"

[filament:PETG ColorFabb 0,25nzl]
inherits = *common*;*PETG-Common*;*PETG ColorFabb*
filament_max_volumetric_speed = 13
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.25
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA025]	;PA for 0.25nzl TODO\nSET_RETRACTION RETRACT_LENGTH=0.6 UNRETRACT_EXTRA_LENGTH=0.0"
filament_settings_id = "PETG_CoFa_025"

[filament:PETG ColorFabb 0,40nzl]
inherits = *common*;*PETG-Common*;*PETG ColorFabb*
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.40
filament_max_volumetric_speed = 13
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA040]	;PA for 0.40nzl TODO\nSET_RETRACTION RETRACT_LENGTH=0.6 UNRETRACT_EXTRA_LENGTH=0.0"
filament_settings_id = "PETG_CoFa_040"

[filament:PETG ColorFabb 0,60nzl]
inherits = *common*;*PETG-Common*;*PETG ColorFabb*
filament_max_volumetric_speed = 20
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.60
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA060]	;PA for 0.60nzl TODO\nSET_RETRACTION RETRACT_LENGTH=0.6 UNRETRACT_EXTRA_LENGTH=0.0"
filament_settings_id = "PETG_CoFa_060"

[filament:PETG ColorFabb 0,80nzl]
inherits = *common*;*PETG-Common*;*PETG ColorFabb*
filament_max_volumetric_speed = 20
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.80
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA080]	;PA for 0.80nzl TODO\nSET_RETRACTION RETRACT_LENGTH=0.6 UNRETRACT_EXTRA_LENGTH=0.0"
filament_settings_id = "PETG_CoFa_080"


[filament:PETG REAL 0,25nzl]
inherits = *common*;*PETG-Common*;*PETG REAL*
filament_max_volumetric_speed = 16
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.25
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA025]	;PA for 0.25nzl TODO\nSET_RETRACTION RETRACT_LENGTH=0.6 UNRETRACT_EXTRA_LENGTH=0.0"
filament_settings_id = "PETG_REAL_025"

[filament:PETG REAL 0,40nzl]
inherits = *common*;*PETG-Common*;*PETG REAL*
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.40
filament_max_volumetric_speed = 29
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA040]	;PA for 0.40nzl TODO\nSET_RETRACTION RETRACT_LENGTH=0.6 UNRETRACT_EXTRA_LENGTH=0.0"
filament_settings_id = "PETG_REAL_040"

[filament:PETG REAL 0,60nzl]
inherits = *common*;*PETG-Common*;*PETG REAL*
filament_max_volumetric_speed = 29
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.60
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA060]	;PA for 0.60nzl TODO\nSET_RETRACTION RETRACT_LENGTH=0.6 UNRETRACT_EXTRA_LENGTH=0.0"
filament_settings_id = "PETG_REAL_060"

[filament:PETG REAL 0,80nzl]
inherits = *common*;*PETG-Common*;*PETG REAL*
;filament_max_volumetric_speed = 20
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.80
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA080]	;PA for 0.80nzl TODO\nSET_RETRACTION RETRACT_LENGTH=0.6 UNRETRACT_EXTRA_LENGTH=0.0"
filament_settings_id = "PETG_REAL_080"

[filament:PETG eSun 0,25nzl]
inherits = *common*;*PETG-Common*;*PETG eSun*
filament_max_volumetric_speed = 9.5
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.25
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA025]	;PA for 0.25nzl TODO\nSET_RETRACTION RETRACT_LENGTH=0.6 UNRETRACT_EXTRA_LENGTH=0.0"
filament_settings_id = "PETG_eSun_025"

[filament:PETG eSun 0,40nzl]
inherits = *common*;*PETG-Common*;*PETG eSun*
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.40
filament_max_volumetric_speed = 21
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA040]	;PA for 0.40nzl TODO\nSET_RETRACTION RETRACT_LENGTH=0.6 UNRETRACT_EXTRA_LENGTH=0.0"
filament_settings_id = "PETG_eSun_040"

[filament:PETG eSun 0,60nzl]
inherits = *common*;*PETG-Common*;*PETG eSun*
filament_max_volumetric_speed = 23
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.60
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA060]	;PA for 0.60nzl TODO\nSET_RETRACTION RETRACT_LENGTH=0.6 UNRETRACT_EXTRA_LENGTH=0.0"
filament_settings_id = "PETG_eSun_060"

[filament:PETG eSun 0,80nzl]
inherits = *common*;*PETG-Common*;*PETG eSun*
filament_max_volumetric_speed = 24
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.80
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA080]	;PA for 0.80nzl TODO\nSET_RETRACTION RETRACT_LENGTH=0.6 UNRETRACT_EXTRA_LENGTH=0.0"
filament_settings_id = "PETG_eSun_080"

[filament:LW-PLA ColorFabb 0,40nzl]
inherits = *common*;*LW-PLA-Common*;*LW-PLA ColorFabb*
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.40
filament_max_volumetric_speed = 35
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA040]	;PA for 0.40nzl TODO\nSET_RETRACTION RETRACT_LENGTH=0.6 UNRETRACT_EXTRA_LENGTH=0.0"
filament_settings_id = "LW-PLA_CoFa_040"

[filament:ASA ColorFabb 0,25nzl]
inherits = *common*;*ASA-Common*;*ASA ColorFabb*
filament_max_volumetric_speed = 14
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.25
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA025]	;PA for 0.25nzl TODO"

[filament:ASA ColorFabb 0,40nzl]
inherits = *common*;*ASA-Common*;*ASA ColorFabb*
filament_max_volumetric_speed = 31
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.40
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA040]	;PA for 0.40nzl TODO"

[filament:ASA ColorFabb 0,60nzl]
inherits = *common*;*ASA-Common*;*ASA ColorFabb*
filament_max_volumetric_speed = 35
filament_custom_variables = "PA025 = 0.075\nPA040 = 0.021\nPA060 = 0.027\nPA080 = 0.006"
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.60
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA060]	;PA for 0.60nzl TODO"

[filament:ASA ColorFabb 0,80nzl]
inherits = *common*;*ASA-Common*;*ASA ColorFabb*
filament_max_volumetric_speed = 39
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.80
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA080]	;PA for 0.80nzl TODO"

[filament:TPU 85A&98A AzureFilm 0,25nzl]
inherits = *common*;*TPU-Common*;*TPU AzureFilm*
filament_max_volumetric_speed = 14 ;NOT CALIBRATED
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.25
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA025]   ;PA for 0.25nzl\n;SET_RETRACTION RETRACT_LENGTH=0.5 UNRETRACT_EXTRA_LENGTH=0.0"


[filament:TPU 85A&98A AzureFilm 0,40nzl]
inherits = *common*;*TPU-Common*;*TPU 85A&98A AzureFilm*
filament_max_volumetric_speed = 30 ;NOT CALIBRATED
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.40
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA040]   ;PA for 0.40nzl\n;SET_RETRACTION RETRACT_LENGTH=0.5 UNRETRACT_EXTRA_LENGTH=0.0"


[filament:TPU 85A&98A AzureFilm 0,60nzl]
inherits = *common*;*TPU-Common*;*TPU 85A&98A AzureFilm*
filament_max_volumetric_speed = 30 ;NOT CALIBRATED
compatible_printers_condition = printer_model=~/.*VC3_.*/ && nozzle_diameter[0]==0.60
start_filament_gcode = "; Filament gcode\nSET_PRESSURE_ADVANCE ADVANCE=[PA060]   ;PA for 0.60nzl\n;SET_RETRACTION RETRACT_LENGTH=0.5 UNRETRACT_EXTRA_LENGTH=0.0"



# How it works
The general setup is such;
The file **can** contain 4 main categories that correspond with the tabs in SuperSlicer;
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
