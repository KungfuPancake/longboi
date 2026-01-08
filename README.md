# Longboi AFC controller
![assembled pcb render](https://github.com/KungfuPancake/longboi/blob/main/images/longboi_v1.0_render.jpg)
Tired of the mess of cables in your BoxTurtle? Want to add a heated filament dryer chamber some time down the road? Want to control that cooling fan with PWM? This project is for you!

The Longboi sits behind the full width of the BoxTurtle front panel and allows very short cable runs of equal length from each respooler and extruder. It's equipped with a beefy STM32H723 MCU and can be controlled with CAN or USB. If using CAN it's also daisy chainable for 8, 12 or even more lanes!

# How to get a Longboi

## Buying a complete board
Manufacturing and sales of complete boards is currently being worked on. Please check later for details.

## PCB + Assembly by JLCPCB
You can order assembled PCBs from pretty much any fab, although currently only production files tested with JLCPCB are available in [jlcpcb/](https://github.com/KungfuPancake/longboi/tree/main/jlcpcb).
To order PCBs with assembly you'll have to go through their manufacturing wizard, using mostly standard settings. Although the JLC engineers will try and correct any mistakes, please do sanity checks on every page.
1. Upload `longboi.zip`into the PCB quote form and select a quantity. Five PCBs is the minimum quantity, cost per board will be lower the more boards you order since most of the cost is setup fees. Select a solder mask color you like - green is usually cheapest. I highly recommend choosing ENIG as a surface finish, HASL is usually not worth the saved money on production boards. Leave all other settings on their default values. Check PCBA (assembly) on the bottom of the form. Click "Next".
2. Check if the PCB looks like it should, then click "Next".
3. Upload `bom.csv` for BOM, `positions.csv`for CPL. Click "Next".
4. Check if all components look right, if there are stock shortages you will have to select replacement. Be very careful to select components that are equal or better in specs. If everything looks good, click "Next".
5. Check the placement of all components, especially if the first pin corresponds with the PCB markings. Slight inaccuracies on the big through hole connectors are okay, as they are hand soldered anyway. Click "Next".
6. You'll see a breakdown of all individual fees and prices. If you're flexible on the amount of PCBs you order you can try and see which amount has the best value to you.
7. Done!

Please be aware that the BOM only contains a fuse holder, the fuses (2410/1808) have to be sourced separately. 4A is a good value to start, depending on the heater you may or may not use the value can be scaled up or down accordingly.

## PCB + assembling it yourself
Order the PCBs anywere you want as the specs are very modest. Assembly of the board is an advanced task requiring lots of soldering, including hot air and/or using a reflow oven. If you don't feel comfortable with that, you should let the fab handle the assembly.

Please be aware that the BOM only contains a fuse holder, the fuses (2410/1808) have to be sourced separately. 4A is a good value to start, depending on the heater you may or may not use the value can be scaled up or down accordingly.

# Preparations
This is a general guideline how to proceed with flashing the Katapult bootloader (optional) and Klipper. If you struggle with the process or encounter errors on the way, especially when using CAN bus, try the detailed instructions and troubleshooting tips and tricks from [Esoterical's CANBus guide][https://canbus.esoterical.online/].
Start with only USB connected via the JST header. When plugging in the USB cable into your computer, the 3.3V and USB LEDs should light up. Now you're ready for flashing!


## CAN
### Katapult
![katapult reference config for can](https://github.com/KungfuPancake/longboi/blob/main/images/katapult_can.png)

### Klipper
![klipper reference config for can](https://github.com/KungfuPancake/longboi/blob/main/images/klipper_can.png)

## USB
### Katapult
![katapult reference config for usb](https://github.com/KungfuPancake/longboi/blob/main/images/katapult_usb.png)

### Klipper
![klipper reference config for usb](https://github.com/KungfuPancake/longboi/blob/main/images/klipper_usb.png)

## Initial flash via DFU
To enable CAN or USB flashing via Katapult or Klipper you'll have to bootstrap the board via the MCUs built-in DFU bootloader, using dfu-util. To enter the DFU bootloader, hold down the "Boot"-button, then press the "Reset"-button shortly. You can now release both buttons and the Longboi should be visible as a "STM Device in DFU Mode" USB device.

Use dfu-util to flash the desired firmware, e.g. Katapult: ```dfu-util -R -a 0 -s 0x08000000:leave -D ~/katapult/out/katapult.bin -d 0483:df11```

## Flashing Katapult and/or Klipper
When using USB, flashing Katapult is not needed as Klipper can update its own firmware. Go through the Klipper build steps using the configuration from above, then flash klipper.bin using dfu-util. From now on you can update Klipper directly with ```make flash FLASH_DEVICE={your device path}```

If you're using a CAN setup and need to use Katapult, build it with the configuration from above and flash it via ```dfu-util```. You may have to reset the board with the "Reset"-button after flashing, the "Info"-LED should be blinking now. If it doesn't, try manually entering Katapult by double-clicking "Reset". Once Katapult is running, all flash operations have to be done via CAN bus. Remove the USB cable from the board and attach it to your CAN bus using a suitable cable. Remember to power down your printer before connecting anything!

If everything went well, Katapult should be visible with the flash tool: ```~/katapult/scripts/flashtool.py -i can0 -q```

You can now flash Klipper: ```~/katapult/scripts/flashtool.py -f ~/klipper/out/klipper.bin -u {your address}```

And you're done!

## Print parts
### Base BoxTurtle
BOM:
* ~95g of primary color filament
* 7x M3x4x5 heat set insert (Voron style)
* 7x M3x10 SHCS
* (Optional) 7x M3x5 brass standoffs
* (Optional) 7x M3x4 SHCS

Print the following parts in your primary color:

* 1x skirt_front_hex_single.stl (no split parts available yet)

If you're not using brass standoffs, also print:

* 7x pcb_spacer_x7.stl

Print all parts using the BoxTurtle settings.

### BoxTurtle Enclosure
Please use the CAD files for reference until a detailed manual is available.

# Assembly
## Mounting PCB + front skirt
### Base BoxTurtle
Add the heat set inserts into the front skirt, then mount the PCB. If you're using brass standoffs, screw those in first, then mount the board using the M3x4 fasteners. If using printed spacers, for easier assembly you can pre-insert two M3x10s into the PCB, add spacers and then mount it on the front skirt. Then use tweezers to add spacers between the PCB and the skirt on the remaining holes and add the rest of the M3x10s.
Insert the front skirt according to the manual and double check that the cutouts on the PCB correspond to the respooler switch tabs.

### BoxTurtle Enclosure
Please use the CAD files for reference until a detailed manual is available.

## Connections
Every respooler + extruder has its own set of connectors on the PCB, e.g. M4, RGB4, S8, S7, STP4.
![pcb render with pinout](https://github.com/KungfuPancake/longboi/blob/main/images/longboi_v1.0_pinout.png)

If you're making your own cables, to make your life easier, assemble one respooler and one extruder first with generous cable length. Mount everything, measure everything, cut and crimp all connections. Afterwards, remove the respooler and extruder and finish the other three sets outside of the BoxTurtle with your measurements.
Tidy up with cable ties/velcro straps as needed and plug in everything.
![mounted pcb](https://github.com/KungfuPancake/longboi/blob/main/images/longboi_v0.1_mounted.jpg)

# Configuration
## Pin aliases / longboi.cfg
coming soon
## AFC configuration
coming soon
