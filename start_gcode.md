###  OrcaSlicer Start G-Code:
```gocde
; ----    START CONDITIONS
G90;
G92 E0.0

; ----    HOME AND WAIT POS
M117 Startup
G28; HOME
START_PRINT BED_TEMP=[bed_temperature_initial_layer_single]
M140 S[bed_temperature_initial_layer_single]
G1 Y0 X0 Z1 F5000
M117 Wait for Bed
M190 S[bed_temperature_initial_layer_single]; wait for bed temp
BEEP
G4 P2000;

; ----     WAIT FOR NOZZLE TEMP
M117 Wait for Nozzle
M109 S[nozzle_temperature_initial_layer] ; wait for extruder temp
BEEP
BEEP
BEEP
G4 P2000;
M117 Prime

; ----    PRIME NOZZLE 
G90
M83
G1 E10 F100
G1 X30 Z[first_layer_height] F1000                                              ; Strip Off Filament on Bed
G1 X{first_layer_print_min[0]+50} Y{first_layer_print_min[1]-2} Z1 F6000        ; Move to Print Object for Prime
G1 Z0.3 F500                                                                    ; Adjust Hight
G1 X{first_layer_print_min[0]} E10 F1000                                        ; Prime Nozzle
M117 Printing
```
