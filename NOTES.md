# NOTES for Sidewinder X1

1. The changes are ported from [bltouch_waggster_mod_(z_max_used)](https://www.thingiverse.com/thing:4294049)

## Useful Links

1. Must check this https://e3d-online.dozuki.com/Guide/V6+Marlin+Configuration/5

## Known Issues

### BLTouch not working

**The problem**

The BLTouch sometimes does not go down and let the hotend crash the bed

This happens especially with Octoprint (mostly fine with TFT screen):

1. In Octoprint, send `G28` through Terminal works fine, BLTouch goes down
2. In Octoprint, print a gcode file **without** temperature setting is fine, e.g.:
    ```gcode
    G29             ; Home
    G29 P1          ; Do automated probing of the bed.
    G29 P3 T        ; Repeat until all mesh points are filled in.
    G29 S1          ; Save UBL mesh points to EEPROM.
    G29 F 10.0      ; Set Fade Height for correction at 10.0 mm.
    G29 A           ; Activate the UBL System.
    M500            ; Save current setup. WARNING: UBL will be active at power up, before any `G28`.
    ```
3. Here's the problem, when Octoprint execute a gcode file **with** temperature setting, once executing `G28`, the BLTouch does not come down and let the hotend crash the bed, or let the printer report `probing failed`. e.g. add temperature
    ```gcode
    ; https://github.com/OctoPrint/OctoPrint/issues/2786
    M190 S70        ; Not required, but having the printer bed at temperature helps accuracy
    G28             ; Home
    G29 P1          ; Do automated probing of the bed.
    G29 P3 T        ; Repeat until all mesh points are filled in.
    G29 S1          ; Save UBL mesh points to EEPROM.
    G29 F 10.0      ; Set Fade Height for correction at 10.0 mm.
    G29 A           ; Activate the UBL System.
    M500            ; Save current setup. WARNING: UBL will be active at power up, before any `G28`.
    ```

**Potential issues**
1. In `Configuration_adv.h`, `BLTOUCH_HS_MODE` is enabled, should test if disable it will help
2. `TX_BUFFER_SIZE` 16 is too small? Because Octoprint uses serial bus communication.
3. `Z_MIN_PROBE_USES_Z_MIN_ENDSTOP_PIN` and nearby changes?
4. `Z_MIN_PROBE_REPEATABILITY_TEST` ?