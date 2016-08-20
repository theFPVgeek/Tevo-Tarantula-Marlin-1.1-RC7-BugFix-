This is the Marlin v1.1 Release Candidate Bugfix firmware as of August 9, 2016.

+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+ IMPORTANT! There are two configuration.h files provided. One with Auto Bed Leveling enabled and one with Auto Bed +
+ Leveling disabled. YOU MUST COPY THE APPROPRIATE CONFIGURATION.H FILE INTO THE MARLIN FOLDER.                     +
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++

Hot end and heated bed PID is enabled, you should run PID autotune.

The configuration.h file with auto bed levelling has been configured for the TEVO Tarantula with a fixed sensor.

This release includes support for multiple extruders. It is not enabled as I can't test this feature.

This release includes support for BLTouch sensor. I don't have one, so can't test. See line 461.

You can now change the LCD controller encoder (knob) direction for menus and for values separately, in configuration.h. See lines 1035 to 1058.

A new feature has been added: Print statistics are now saved. The statistics can be read out with M78 command. (Verbose mode may need to be enabled in your host program.) See lines 930 to 939 in configuration.h

Don't forget to send M502 followed by M500 after uploading to update EEPROM settings!

Use at your own risk!

You will need to modify several settings in configuration.h for your specific printer:

Lines 588 to 591: Set your axis directions. They are currently set to match the build in Ed's videos. Should be fine for most, but some may need to change Y axis direction.

Lines 618 to 624: Set your bed dimensions. (For large bed option, change line 507 to "#define Y_MAX_POS 280")

Lines 731 to 735: Set your home position offsets. See How to set home position offsets below.

Note that EEPROM is enabled. If you do not wish to use EEPROM, comment out line 798.

Lines 489 to 491: If you are using a Z-Probe, set your offsets here. You can leave the Z offset at 0, see comments about M851 below.

If you are using Auto Bed Levelling and you have a fixed sensor attached to the hot end, you should be good to go (after making the changes described above. If you have some other kind of sensor (servo type, sled, etc) read the comments starting at line 441.

Caution! Marlin v1.1 has fixed a glitch in the G92 command. This glitch was used by some to figure out a Z probe offset. It is no longer possible to use G92 to find a Z probe offset. See below for instructions.

Marlin v1.1 has implemented a new custom M code for the Z offset, M851. Syntax is M851 Z<offset> where <offset> is the difference between were the sensor detects the bed and the distance the nozzle is from the bed. It should always be negative for fixed sensor probes. To use follow these steps:

If you are using Repetier-host to control your printer, send M851 Z<same value as Z_MIN_POS> to temporarily set an offset.

Do a G28 to home all axis. 

Do a G1 X100 Y100 to move the nozzle to the middle of the bed.

Place a piece of paper between the nozzle and the bed.

Use your host programs printer controls to lower the Z axis .1 mm at a time.

When you feel some resistance to moving the paper, make a note of the Z position in your host program. (It should be negative. If you are using Repetier-Host, subtract the Z_MIN_POS value you used above.)

Send M851 Z<number> to the printer.

Send M500 to save the offset to EEPROM.

Try a print.

If you need to adjust the Z offset because it is too close or too far away from the bed, simply send another M851 Z<number> with the new offset you want to try, followed by M500. Lather, rinse, repeat until you have the offset dialed in.

Note: due to various environmental factors, the Z offset can change from day to day. If you start a print on a new day and find the Z offset is not right, simply send M851 Z<new offset> followed by M500 until it is good again.

Have fun!
...jim

How to set home position offsets
================================

First, make sure you have the correct bed dimensions defined:

// Travel limits after homing (standard bed)
#define X_MIN_POS 0
#define Y_MIN_POS 0
#define Z_MIN_POS 0
#define X_MAX_POS 200
#define Y_MAX_POS 200
#define Z_MAX_POS 200

(Note: for large bed, use #define Y_MAX_POS 280)

Second, adjust your manual axis home positions:

#define MANUAL_X_HOME_POS 0
#define MANUAL_Y_HOME_POS 0
#define MANUAL_Z_HOME_POS 0

The values for the manual home position depend on the location of your X and Y endstops. To figure out what those values should be, set them to 0 for now, compile and upload the firmware.

Place a mark in the exact center of your bed.

Home your X and Y axis.

Send G1 X100 Y100 (G1 X100 Y140 for large bed) to the printer. This will move the nozzle to what the printer thinks is the center of your bed.

If it is not right above the mark you made, use the jog controls to move the nozzle 1mm at a time until it is as close to being over the mark on the bed as it can.

Note the values of the X and Y axis.

Subtract 100 from those values and change the sign (change positive to negative, negative to positive) and plug them in to the manual axis home position above. (Normally, these should be negative values, if your home direction is min (-1).)

Compile and load the firmware. (Don't forget M502 and M500).

Home X and Y and send G1 X100 Y100 (G1 X100 X140 for large bed).

Your nozzle should now be right over the center mark.

