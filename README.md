# Sovol-SV08-Tweaks

Dieses Repo enthält Optimierungen und Makros für den Sovol SV08.

## Inhaltsverzeichnis
- [START_PRINT](#start_print)
- [END_PRINT](#end_print)
- [CLEAN_NOZZLE](#clean_nozzle)
- [Tapping & Offset](#tapping--offset)

---

## Makros

### START_PRINT
Das `START_PRINT` Makro kümmert sich um:
- Homing
- Aufheizen (Bett & Nozzle)
- Nozzle Reinigung
- Heatsoak (optional)
- Quad Gantry Leveling (QGL)
- Tapping (Z-Offset Korrektur)
- Bed Mesh (Adaptive)

Die Datei findest du hier: [`macros/start_print.cfg`](macros/start_print.cfg)

### END_PRINT
Das `END_PRINT` Makro kümmert sich um:
- Retract & Wipe
- Abkühlen
- Abschalten der Lüfter und Motoren

Die Datei findest du hier: [`macros/end_print.cfg`](macros/end_print.cfg)

### CLEAN_NOZZLE
Ein spezielles Makro zur Reinigung der Nozzle an der Bürste.
- Heizt auf 220°C auf (falls nötig)
- Führt eine Wischbewegung über die Bürste aus

Die Datei findest du hier: [`macros/nozzle_clean.cfg`](macros/nozzle_clean.cfg)

### Tapping & Offset
Enthält Logik für die Z-Offset Korrektur nach dem Tapping (`_FINISH_TAP_CORRECTION`) und weitere Kalibrierungs-Makros.

Die Datei findest du hier: [`macros/tapping.cfg`](macros/tapping.cfg)

---

## Weitere Dokumentationen
- [Probe with Print Temp](probe_with_print_temp.md)
- [Start Gcode](start_gcode.md)
- [Tune Filamentsensor](tune_filamentsensor.md)
- [BTT Smart Filament Sensor](btt-smart-filament-sensor/readme.md)
