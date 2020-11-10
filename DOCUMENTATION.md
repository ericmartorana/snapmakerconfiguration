# Slynolds snapmakerconfiguration

# Documentation

# Base settings

## Speed Settings

Based on https://support.snapmaker.com/hc/en-us/articles/360044341034-What-is-the-recommended-3D-printing-settings-in-Cura-or-Simplify3D-for-Snapmaker-2-0-
(10.11.2020)

The recommended print speed is less than 60 mm/s, the recommended travel speed is less than 80 mm/s, and the recommended initial layer speed is less than 24 mm/s.

## Acceleration Settings

The official cura profiles limit acceleration to 1000 mm/s via cura acceleration control settings.
This option uses deprectated gcode, so I limited the acceleration in my start gcode section via following gcode line.
```
M204 T1000 P750; Limit accelleration to manufacturer advice
```

# Start gcode

# End gcode

# profiles

## Custom fine

## Custom regular

## Custom coarse

## Custom draft