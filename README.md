# Slynolds snapmakerconfiguration

This repository contains cura configuration for the Snapmaker A350

I'm a beginner in 3d printing.
Curas configuration possibilitys are overwhelming, so created this repository mainly to keep track of, and document my configuration changes.

I did not use the profiles provided by the Snapmaker team, because I did not understand them fully.

Curas default configuration profiles contain functions to determine values that are general useful for all fdm printers.
(For example the speed settings for inner and outer walls)
Once overwritten it's difficult to restore these functions for settings. That's why I started from scratch to create profiles for my printer.

Apart from that, while creating these profiles, I tried to consider manufacturers advice, but I cannot guarantee to adhere to them.

I am not responsible for damaged printers and failed prints
Please do some research if you have any concerns about settings in these configuration files.


# Speed Settings

The recommended print speed is less than 60 mm/s, the recommended travel speed is less than 80 mm/s, and the recommended initial layer speed is less than 24 mm/s.

# Acceleration Settings

The official cura profiles limit acceleration to 1000 mm/s via cura acceleration control settings.
This option uses deprectated gcode, so I limited the acceleration in my start gcode section via following gcode line.
```
M204 T1000 P750; Limit accelleration to manufacturer advice
```

Feel free to create branches with proposed changes and optimisations.

