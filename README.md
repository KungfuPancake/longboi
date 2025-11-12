# Longboi AFC controller
![assembled pcb render](https://github.com/KungfuPancake/longboi/blob/main/images/longboi_v1.0_render.jpg)
Tired of the mess of cables in your BoxTurtle? Want to add a heated filament dryer chamber some time down the road? Want to control that cooling fan with PWM? This project is for you!

The longboi sits behind the full width of the BoxTurtle front panel and allows very short cable runs of equal length from each respooler and extruder. It's equipped with a beefy STM32H723 MCU and can be controlled with CAN or USB. If using CAN it's also daisy chainable for 8, 12 or even more lanes!

# How to get a Longboi
## Buying a complete board
There is no way to buy a complete board right now. A limited production run might be possible in the future. Please check back later!

## Making your own
Getting your own PCB made is pretty easy nowadays. It's a 4-layer board with moderate dimensions and spacings, so pretty much every manufacturer can handle this. You can use KiCad to create Gerber files or use the premade files in the repository. Always double check the output with the manufacturers visual tools!
Assembly of the board is an advanced task requiring lots of soldering, including hot air and/or using a reflow oven. If you don't feel comfortable with that, most of the popular PCB manufacturers will assemble the boards for you, for a fee. All the parts used on the board have a LCSC part ID assigned to them, for easy ordering and/or assembly at JLCPCB.

# Getting started
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

## Connections
![pcb render with pinout](https://github.com/KungfuPancake/longboi/blob/main/images/longboi_v1.0_pinout.png)
![mounted pcb](https://github.com/KungfuPancake/longboi/blob/main/images/longboi_v0.1_mounted.png)
