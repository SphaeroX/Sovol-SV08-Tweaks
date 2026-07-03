# Sovol-SV08-Tweaks

This repository contains various optimizations, macros, and hardware modifications for the **Sovol SV08** 3D printer. 

It is structured to keep your Klipper configuration modular, efficient, and up-to-date with the latest Klipper features (such as BTT Eddy `descend_z` and native `METHOD=tap` updates).

## Repository Structure

```
Sovol-SV08-Tweaks/
├── btt-smart-filament-sensor/  # Guide for installing the BTT Smart Filament Sensor V2.0
│   ├── btt_v2_wire.jpg         # Wiring diagram for the sensor
│   └── readme.md               # Step-by-step installation & configuration guide
├── macros/                     # Modular Klipper macro configurations
│   ├── start_print.cfg         # START_PRINT macro logic (Homing, heating, mesh, etc.)
│   ├── end_print.cfg           # END_PRINT macro logic (Wipe, retraction, cooldown, fans off)
│   ├── nozzle_clean.cfg        # CLEAN_NOZZLE macro logic (Heating, purging, cooling, wiping)
│   └── tapping.cfg             # Tapping & Z-offset logic (Z-offset corrections, compatibility layer)
├── README.md                   # Main documentation (this file)
└── tune_filamentsensor.md      # Guide for reducing friction in the stock filament sensor
```

---

## Macro Details

### 1. START_PRINT
The [`START_PRINT`](macros/start_print.cfg) macro handles the entire print preparation workflow:
- **Homing**: Homes all axes if not already homed.
- **Heating**: Simultaneously heats the bed and nozzle to target temperatures.
- **Nozzle Cleaning**: Runs the `CLEAN_NOZZLE` macro to clean the nozzle tip before probing.
- **Heatsoak**: Performs an optional heat soak based on configured duration.
- **Quad Gantry Leveling (QGL)**: Corrects gantry twist/alignment if not already applied.
- **Tapping**: Performs Z-offset probing using `PROBE METHOD=tap` (supporting adaptive center positioning or bed center homing).
- **Bed Mesh**: Generates an adaptive bed mesh using a rapid scan to match the printed object footprint.

File path: [`macros/start_print.cfg`](macros/start_print.cfg)

### 2. END_PRINT
The [`END_PRINT`](macros/end_print.cfg) macro cleanly finishes the print:
- **Retract & Wipe**: Performs a retraction and wipe sequence to prevent nozzle oozing/blobs.
- **Cooldown**: Cools down both the hotend and bed heaters.
- **Fans & Motors**: Powers down all fans (exhaust fan turns off after a delayed cooldown) and disables stepper motors.

File path: [`macros/end_print.cfg`](macros/end_print.cfg)

### 3. CLEAN_NOZZLE
The [`CLEAN_NOZZLE`](macros/nozzle_clean.cfg) macro is a highly optimized nozzle cleaning routing:
- **Purge**: Heats the nozzle to 220°C (if below) and performs an initial 10mm purge over the brush to clear leftover plastic, followed by a relief retraction to prevent oozing.
- **Cooling Wipe**: Lowers the nozzle temperature target to 150°C and starts wiping iterations over the brush. Wiping while the nozzle cools prevents new ooze from forming on the clean nozzle.
- **"The Snap"**: Finishes with a final retraction and a fast Z-lift ("snap") to break off any remaining filament strings.

File path: [`macros/nozzle_clean.cfg`](macros/nozzle_clean.cfg)

### 4. Tapping & Offset
The [`tapping.cfg`](macros/tapping.cfg) configuration contains the macros and correction logic for nozzle-based Z-offset mapping:
- **`_FINISH_TAP_CORRECTION`**: Sets kinematic position based on last probe height and applies Z-offset.
- **`Z_OFFSET_APPLY_PROBE`**: Custom wrapper configured for the modern Klipper Eddy tap method (`METHOD=tap`).
- **`SET_Z_FROM_PROBE`**: Safe wrapper macro that dynamically falls back between the deprecated `z_offset` and the new Klipper `descend_z` parameters to avoid config errors.
- **`_CALIBRATION_ZOFFSET`**: Automated Z-offset calibration sequence.

File path: [`macros/tapping.cfg`](macros/tapping.cfg)

---

## Hardware Guides

### stock Filament Sensor Tuning
The stock filament sensor can have high friction due to unnecessary internal brass inserts. The guide shows how to remove them to ensure smoother filament feeding.
- Read the instructions here: [`tune_filamentsensor.md`](tune_filamentsensor.md)

### BIGTREETECH Smart Filament Sensor V2.0
If you want to upgrade your filament sensor for clog/tangle detection, follow this installation guide detailing the pin modifications, configuration replacements in Klipper, and wiring details.
- Read the setup guide here: [`btt-smart-filament-sensor/readme.md`](btt-smart-filament-sensor/readme.md)
- Wiring schematic: [`btt-smart-filament-sensor/btt_v2_wire.jpg`](btt-smart-filament-sensor/btt_v2_wire.jpg)

---

## Troubleshooting

### Klipper Eddy Tapping Errors (After Klipper Update)
If you perform a Klipper update and receive the following error when tapping:
```
Unable to detect tap: invalid depress distance (0.339367 vs 0.030000:0.250000)
```
This is caused by outdated or missing calibration values for the Eddy current probe's tapping threshold. You will need to recalibrate your probe.

#### Resolution Steps:

1. Run the initial calibration guessing routine:
   ```
   PROBE_EDDY_CURRENT_TAP_CALIBRATE TAP=guess
   ```
   > [!NOTE]
   > If you encounter the following error on your first attempt:
   > `Unable to detect tap: insufficient slope delta (-167.188057 vs 727.299000)`
   > Simply run the command a second time. It should succeed on subsequent attempts.

2. Refine and verify the calibration:
   ```
   PROBE_EDDY_CURRENT_TAP_CALIBRATE TAP=refine
   PROBE_EDDY_CURRENT_TAP_CALIBRATE TAP=verify
   ```

3. Save the configuration:
   The verification step will yield an output like:
   ```
   probe_eddy_current btt_eddy: tap_threshold: 1324.090
   The SAVE_CONFIG command will update the printer config file
   with the above and restart the printer.
   ```
   Save and apply the changes by running:
   ```
   SAVE_CONFIG
   ```
