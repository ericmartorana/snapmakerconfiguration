# Slynolds snapmakerconfiguration

# Documentation

# Base settings

## Speed Settings

Based on <https://support.snapmaker.com/hc/en-us/articles/360044341034-What-is-the-recommended-3D-printing-settings-in-Cura-or-Simplify3D-for-Snapmaker-2-0->
(10.11.2020)

The recommended print speed is less than 60 mm/s, the recommended travel speed is less than 80 mm/s, and the recommended initial layer speed is less than 24 mm/s.

## Acceleration Settings

The official cura profiles limit acceleration to 1000 mm/s² via cura acceleration control settings. For travel moves this is okay, for extruding moves mm/s² is better.
This option in cura uses deprectated gcode, so I limited the acceleration in the start gcode section via following gcode line.

    M204 T1000 P750; Set accelleration

## Linear advance

While testing with benchy and the overhang settings, I noticed some blobs and a seam where the speed change occured.
To improve this I calibrated linear advance with the marlin calibration pattern https://marlinfw.org/tools/lin_advance/k-factor.html
I determined that a value of 0.7 is a good K-factor.

# Start gcode

This start gcode based on the example proposed by the Snapmaker support article, and optimised for cura

    M104 S{material_print_temperature} T0 ; Set hotend temperature of hotend number 0
    M140 S{material_bed_temperature_layer_0} ; Set temperature of bed for first layer
    M204 T1000 P750; Set acceleration
    G28 ; Auto home all axes
    G90 ; Set to absolute positioning
    G0 X330 Y-10 F3000 ; Move to the outer right front with 50 mm/s
    G0 Z0 F1200 ; Move downwards to Z zero with 20 mm/s
    G92 E-1 ; Set extruder postion to -1
    M109 S{material_bed_temperature_layer_0} T0 ; Wait for temperature of bed
    M190 S{print_bed_temperature} ; Wait for temperature of hotend
    G1 E1 F200 ; Output 1 mm of filament with 3 mm/s

## Summary:

It starts with setting the nozzle and bed temperature defined for the chosen material.
* M104 <https://marlinfw.org/docs/gcode/M104.html>
* M140 <https://marlinfw.org/docs/gcode/M140.html>

After that the acceleration will be limited using the P and T parameters.
It is possible to do that in cura settings but this uses deprecated parameters.
The values are in mm/s²
* M204 <https://marlinfw.org/docs/gcode/M204.html>

Home all axes
* G28 <https://marlinfw.org/docs/gcode/G028.html>

Set absolute positioning. This is the Marlin default, but the slicer cannot guarantee that a previously run gcode file, oder a canceled print didn't leave the printer in relative mode (G91)
* G90 <https://marlinfw.org/docs/gcode/G090.html>

Move down to the outer right front with 3000 mm/m (50 mm/s)
In my setup the printer stands at the left wall on a big desk, so the left side is not easy reachable.
Because of that, I set the configuration to X330.
G0 is used to indicate this is a non extruding movement
* G0 <https://marlinfw.org/docs/gcode/G000-G001.html>

Set extruder position to -1.
* G92 <https://marlinfw.org/docs/gcode/G092.html>

Wait for nozzle and bed temperature defined for the chosen material.
* M109 <https://marlinfw.org/docs/gcode/M109.html>
* M190 <https://marlinfw.org/docs/gcode/M190.html>

Output 1 mm of filament with a feedrate of 200. Now the extuder is at position 0. Another G92 E0 is not required anymore.
* G1 <https://marlinfw.org/docs/gcode/G000-G001.html>



# End gcode

    G90 ; Set to absolute positioning
    G92 E1 ; Set extuder position to 1, to retract with the following step
    G1 E0 F200 ; Retract 1 mm with a feedrate of 3 mm/s, to release nozzle pressure
    M104 S0 ; turn off extruder
    M140 S0 ; turn off bed
    G28 Z; Home Z Axis
    M84 ; disable steppers


## Summary

The end gcode is also based on the Snapmaker support article, with a few changes regarding homing, retraction and end position.
Most required gcodes are documented above.

Disable Steppers stops all Motors in the Printer System
* M84 <https://marlinfw.org/docs/gcode/M018.html>


# Profiles

These profiles don't configure retraction speed and distance because it's set in the material section of cura.
All profiles are build upon the Custom regular profile, so in the other ones, I will only document the differences to Custom regular.

## Custom regular

This profile is for normal prints, where extreme details won't matter.

- layer_height = 0.16
- layer_height_0 = 0.16

- infill_sparse_density = 10

- infill_pattern = lines

The infill density is at 10%. It's not the best but okay if you don't need sturdyness.
The pattern lines seems to be better for skins.

- speed_travel = 80

This limited because of the manufacturers advice.

- travel_avoid_supports = True

This reduces the possibility for the nozzle to hit against the support structure
The impact on printing times is minimal.

- retraction_combing = noskin
- retraction_combing_max_distance = 0.1

This disables combing on the skin, and forces a retraction when moving around inner parts of the model.

- infill_wall_line_count = 1
- zig_zaggify_infill = True

This creates an extra perimeter for the infill.
The whole object now has 3 walls with the inner wall connected seamlessly with the infill.
This results in a cleaner and sturdier infill.

- adhesion_type = skirt
- skirt_line_count = 4

This is for priming.
Also its possible to see if the bed level is good enough for the print area.
With four lines there is also enough time to correct the z-offset if this is nescessary.

- wall_overhang_angle = 30
- wall_overhang_speed_factor = 75

To mitigate problems with overhangs, caused by insufficent cooling is use this setting to slow down on overhangs.
I determined this angle value with some calibration prints.

## Custom fine

This profile is for fine prints where the layers should be almost hidden, and small details are visible.
It's the most problematic setting where i haven't been able to elimnate rough wall decals on some parts.
Possibly an cooling issue.

- layer_height = 0.08
- layer_height_0 = 0.12

Set the first layer to 0.12 mm for better bed adhesion and add the following layers with 0.08 mm height.


## Custom coarse

This profile is for rough prints. It's fast and most details are lost.

- layer_height = 0.24
- layer_height_0 = 0.24

This layer height is very good visible.

- infill_pattern = default

The infill pattern is not fixed in this profile.

- speed_print = 60
- speed_travel = 80

The speed is at maximum advised value.

- wall_overhang_speed_factor = 50

60 mms/s is faster than 40 mm/s for fine and regular, so this value needs to be lower on this profile.
## Custom draft

This profile is for printing a fast draft of the object.

- layer_height = 0.32
- layer_height_0 = 0.32

The layer height is four times of the Custom fine profile and really thick.

- infill_pattern = default

The infill pattern is not fixed in this profile.

- speed_print = 60
- speed_travel = 80
- speed_wall = 60
- speed_wall_x = 60

Inner and outer wall speeds are at the maximum advised printing speed.

- infill_sparse_density = 5

The infill densitity is extreme low, to speed up printing.

- wall_overhang_speed_factor = 50

60 mms/s is faster than 40 mm/s for fine and regular, so this value needs to be lower on this profile.

## Custom adaptive

This profile should combine advantages of fine layers where details matter, and thick layers where wall details won't.
But, for different layer heights, it could be usefull to use different temperature and speed settings.
So it's nescessary to change all values in the need of the print object.

- layer_height = 0.20
- layer_height_0 = 0.20
- adaptive_layer_height_enabled = True
- adaptive_layer_height_threshold = 0.6
- adaptive_layer_height_variation = 0.08
- adaptive_layer_height_variation_step = 0.04

The layer height varies between 0.12 and 0.28, in steps of the nozzle diameter 0.04.

- infill_pattern = default

The infill pattern is not fixed in this profile.

- speed_print = 60
- speed_travel = 80

The speed setting should be changed.
For simple models this fast could be okay, but detailes object with many layer height changes could be printed besser with slow feed rates.

- wall_overhang_speed_factor = 50

60 mms/s is faster than 40 mm/s for fine and regular, so this value needs to be lower on this profile.

# Hints

## Line width

According to following article, it's possible to create a better line adhesion if you increase the line width when you add the nozzle size to it.
https://dyzedesign.com/2018/07/3d-print-speed-calculation-find-optimal-speed/

I made a few tests, which show that printing time decreases, because the standard wall width is reached early and the slicer omits walls. Line adhesion and overhangs are better. Unfortunately wall details are lost, and on some edges get ugly blobs.

Usefull for sturdy prints with simple walls.

## Curling

Thin layers tend to curl up because of thermal expansion.
On the next layer it's possible that the nozzle hits against it, and causes artifacts on the outer wall. In the worst case it could break small parts.
I you have the default cooling solution of the snapmaker, it's hard too find a good combination of material temperature and speed settings.

A good solution was for me to lower temperatures and to speed up printing.

## Overhangs

After a few tests I recognized that there are some problems with overhang angles above 30° on small printable objects. To resolve these.

