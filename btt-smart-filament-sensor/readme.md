# BIGTREETECH Smart Filament Sensor V2.0 Installation Guide for SV08

This guide explains how to replace the original filament sensor on the SV08 with the BIGTREETECH Smart Filament Sensor V2.0.

## Advantages

The BIGTREETECH Smart Filament Sensor V2.0 offers the following benefits:

- Detects filament runout
- Identifies clogged nozzles
- Detects filament leaks
- Recognizes "tangled" filament
- Identifies extruder malfunctions or disturbances

## Installation Guide

1. Wire the sensor: Only connect the encoder as shown in the image [btt_v2_wire.jpg](btt_v2_wire.jpg) (located in the same folder).

2. Edit the `printer.cfg` file:

Remove the original section:

```
[filament_switch_sensor filament_sensor]
pause_on_runout: True
event_delay: 3.0
pause_delay: 0.5
switch_pin: PE9
```

Replace it with:

```
[filament_motion_sensor encoder_sensor]
switch_pin: PE9
detection_length: 2.88
extruder: extruder
pause_on_runout: False
runout_gcode:
  PAUSE
  M117 Filament encoder runout
insert_gcode:
  M117 Filament encoder inserted
```

Note: The `detection_length` can be adjusted as desired. The value above is the original setting.

3. Edit the `macro.cfg` file:
   Open the `macro.cfg` file and replace all instances of `filament_switch_sensor filament_sensor` with `filament_motion_sensor encoder_sensor`.

4. Restart your printer, and you're done!
