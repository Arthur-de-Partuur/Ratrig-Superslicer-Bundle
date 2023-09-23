# Ratrig-Superslicer-Bundle
My SuperSlicer .ini file to be loaded in the Vendor folder

# Introduction
I modified the Ratrig.ini file that can be found in the %appdata%\SuperSlicer\vendor directory (go to "Help" and click "Show configuration folder" to easily locate this file), assuming you have previously installed the Ratrig printer from the configuration menu.

As I struggled with maintaining and aligning various parameters in between different print and printer profiles for my atrig VCore-3.1-300 3D-printer, I read an article on [Hackaday](https://hackaday.com/2022/10/18/3d-printering-managing-multiple-printing-profiles/) about SuperSlicer's Bundles . Althoug I do not fully grasp what this user is actually exporting, I started editing a copy of my Ratrig.ini file. Slowly I started to understand what the user is writing about.

# How it works
The general setup is such;
The file **can* contain 4 main categories that correspond with the tabs in SuperSlicer;
- [printer_model:MDL]
- [print:####]
- [printer:XYZ]
- [filament:ABC]

The beauty of this setup, is that you can create nested sections within each main category. So assuming you want 3 print profiles, each with a lot of generic stuff, but each its own layer height, you can create a common section (that you will need to put between two asterixes (*). This `*common*` file is **NOT** shown in SuperSlicer due to the asteriks. The first entry is the printer_model (speaks for it self). The second entry is then:
```
[print:*common*]
allow_empty_layers = 0
avoid_crossing_not_first_layer = 1
avoid_crossing_perimeters = 0
avoid_crossing_perimeters_max_detour = 0
bottom_fill_pattern = monotonic
...
ALL YOUR "GENERIC" STUFF HERE
...
...
layer_height = 0.15
...
wipe_tower = 0
wipe_tower_bridging = 10
wipe_tower_no_sparse_layers = 0
wipe_tower_rotation_angle = 0
wipe_tower_width = 60
wipe_tower_x = 170
wipe_tower_y = 140
```
The third item is then specific or deviation from the `*common*` section. I do **not** put this between * as I **DO** want to show this in SuperSlicer. As I would need to have this profile contain everything mentioned in it as well as everything that is in `*common*`, it should start with *inherits*
```
[print:0.2mm NORMAL 0.4mm nozzle]
inherits = *common*
layer_height = 0.2
```
As you can see, both `*common*` and `0.2mm NORMAL 0.4mm nozzle` contain the variable `layer_height`, however, as the profile starts with `*common*` and is then overruled by the new `layer_height` entry, the end result is that you have a print profile in SuperSlicer named `0.2mm NORMAL 0.4mm nozzle` that actualy has a layer_height of 0.2mm

The beauty of this system is that you only have to add below simple lines to create in total 3 print profiles with different layer height, whereby you only have to change the `*common*` section in one place if so required. You do not have to edit 3 profiles if you believe a certain setting (like for instance `avoid_crossing_perimeters_max_detour = 0` should be changed to `avoid_crossing_perimeters_max_detour = 1`):
```
[print:0.3mm NORMAL 0.4mm nozzle]
inherits = *common*
layer_height = 0.3
[print:0.35mm NORMAL 0.4mm nozzle]
inherits = *common*
layer_height = 0.35
```

Obviously, the layer height is just an example. You can go bonkers with it and use 
