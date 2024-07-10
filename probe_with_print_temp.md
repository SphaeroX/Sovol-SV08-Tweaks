
# Modifications to START_PRINT and related macros

To call START_PRINT with the bed temperature as a parameter and use this temperature for leveling, we need to make several changes. Here are the necessary modifications:

## 1. Modify START_PRINT:

```gcode
[gcode_macro START_PRINT]
description: Start the print with bed temperature as parameter
variable_state: 'Prepare'
gcode:
    {% set BED_TEMP = params.BED_TEMP|default(65)|float %}

    M400

    CLEAR_PAUSE

    G90
    {% if state == 'Prepare' %}
        {action_respond_info("Prepare!")}

        {% if printer.toolhead.homed_axes|lower != "xyz" %}
            G28
        {% endif %}

        {% if printer['filament_switch_sensor filament_sensor'].enable == True and
              printer['filament_switch_sensor filament_sensor'].filament_detected != True
        %}
            M117 No filament!!!
            {action_respond_info("Please Insert filament in Sensor!")}
            CANCEL_PRINT
        {% endif %}

        {action_respond_info("Check Heating!")}

        M140 S{BED_TEMP}

        {% if printer.heater_bed.temperature < BED_TEMP %}
            M117 Bed heating...
            {action_respond_info("Bed heating...")}
            M190 S{BED_TEMP}
        {% endif %}


        {% if printer.quad_gantry_level.applied|lower != 'true' %}
            QUAD_GANTRY_LEVEL BED_TEMP={BED_TEMP}
        {% endif %}
        BED_MESH_CALIBRATE ADAPTIVE=1 BED_TEMP={BED_TEMP}
        
        SET_GCODE_VARIABLE MACRO=START_PRINT VARIABLE=state VALUE='"Start"' 
        UPDATE_DELAYED_GCODE ID=_print_start_wait DURATION=0.5

    {% elif state == 'Start' %}
        M117 Printing now!!!
        {action_respond_info("Start!")}
    {% endif %}
```

## 2. Modify QUAD_GANTRY_LEVEL:
```gcode
[gcode_macro QUAD_GANTRY_LEVEL]
rename_existing: QUAD_GANTRY_LEVEL_BASE
gcode:
    {% set BED_TEMP = params.BED_TEMP|default(65)|float %}

    {action_respond_info("Check Heating!")}
    {% if printer.heater_bed.temperature != BED_TEMP %}
        M140 S{BED_TEMP}
        {action_respond_info("The bed target temperature was not reached!")}
        {action_respond_info("Bed heating...")}
        M190 S{BED_TEMP}
    {% endif %}

    {% if printer.toolhead.homed_axes|lower != "xyz" %}
        G28
    {% endif %}

    QUAD_GANTRY_LEVEL_BASE

    {% if printer.heater_bed.target == 0 %}
        M140 S0
    {% endif %} 
```

## 3. Modify BED_MESH_CALIBRATE:
```gcode
[gcode_macro BED_MESH_CALIBRATE]
rename_existing: BED_MESH_CALIBRATE_BASE
gcode:
    {% set BED_TEMP = params.BED_TEMP|default(65)|float %}

    {action_respond_info("Check Heating!")}
    {% if printer.heater_bed.temperature != BED_TEMP %}
        M140 S{BED_TEMP}
        {action_respond_info("The bed target temperature was not reached!")}
        {action_respond_info("Bed heating...")}
        M190 S{BED_TEMP}
    {% endif %}

    {% if printer.toolhead.homed_axes|lower != "xyz" %}
        G28
    {% endif %}

    BED_MESH_CLEAR
    
    BED_MESH_CALIBRATE_BASE ADAPTIVE=1

    {% if printer.heater_bed.target == 0 %}
        M140 S0  
    {% endif %}
```

## 4. Add to Start GCode:
```gcode
START_PRINT BED_TEMP=[bed_temperature_initial_layer_single]
```

