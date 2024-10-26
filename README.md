# Ender 6 Klipper Guide

  * [Summary](#summary)
  * [Instructions](#instructions)
    + [Step 1 - Printing and Purchasing](#step-1---printing-and-purchasing)
      - [Parts to Print](#parts-to-print)
      - [Parts to Purchase](#parts-to-purchase)
    + [Step 2 - Flashing the Printer’s Microcontroller](#step-2---flashing-the-printers-microcontroller)
    + [Step 3 - Setting up Klipper on the Raspberry Pi](#step-3---setting-up-klipper-on-the-raspberry-pi)
    + [Step 4 - Installing the Replacement Touchscreen](#step-4---installing-the-replacement-touchscreen)
    + [Step 5 - Installing the Raspberry Pi and Buck Converter](#step-5---installing-the-raspberry-pi-and-buck-converter)
    + [Step 6 - Configuring the Z-Offset and Bed Mesh](#step-6---configuring-the-z-offset-and-bed-mesh)
    + [Step 7 - Configure Macros and Slicer](#step-7---configure-macros-and-slicer)
    + [Optional Steps](#optional-steps)
      - [Configure a Webcam](#configure-a-webcam)
      - [Configure a USB Wireless Adapter](#configure-a-usb-wireless-adapter)
      - [Print a Klipper Benchy](#print-a-klipper-benchy)

<hr>

## Summary

After around a week of work and multiple parts orders and prints, I just finished converting my Ender 6 to use Klipper. I found that a lot of the information available online is out of date, so I decided to write this guide so that others might have an easier time in the future.

With this guide, you'll be able to retain the use of the touch screen housing, but you will need to replace the screen itself. I install the Raspberry Pi inside the electronics compartment below the printer which unfortunately reduces wifi quality. You may wish to install a wirelss USB adapter with an external antenna if this is a problem for you.

:warning: TODO: Photo of printer with Benchy

<hr>

## Instructions

### Step 1 - Printing and Purchasing

Before you flash anything to your printer or take anything apart, you need to use it to print a few parts.

#### Parts to Print

- Raspberry Pi & Buck Converter Mount: [here](https://www.thingiverse.com/thing:6273981)
  - Since Klipper runs on a Raspberry Pi which you will power via the printer's power supply, you need a place to install the new componenets. I chose the electronics compartment below the printer. This model does not shield the buck converter, so take care when handling it as there is a risk of being shocked.

- PIT TFT43 Screen Adapter: [here](https://www.thingiverse.com/thing:5246155)
  - KlipperScreen does not work with the default touchscreen for the Ender 6. There is a fork floating around that adds support, but it is unmaintained and the code was never merged upstream. In order to preserve the functionality of the touchscreen, you must replace it with one that is compatible. I chose the `PI TFT43` because it fits well in teh housing. However, you will need to print an adapter to mount it correctly.

- Optional: Gantry Fan Mount: [here](https://www.thingiverse.com/thing:6784980)
  - Should you wish you provide cooling to the Raspberry Pi and Buck Convert, you can print this model I made that allows you to mount a Noctua NF-A4x10 fan on the gantry rails.

- Optional: 3x M3 T-Nuts: [here](https://www.thingiverse.com/thing:3050607)
  - These are only needed if you wish to save a little bit of money and avoid purchasing them. But you need a way to mount the Raspberry Pi/Buck Converter part on the rails in your electronics compartment.

#### Parts to Purchase

- Wifi Capable Raspberry Pi: [here](https://www.amazon.com/dp/B0CPWH8FL9)
  - I bought the 5, you can probably get away with something cheaper if you wish.

- MicroSD Card

- BIGTREETECH PI TFT43 Screen: [here](https://www.amazon.com/dp/B09791ZG1B)

- Buck Converter: [here](https://www.amazon.com/dp/B07WQJ2GD6)

- 50CM 22Pin -> 15Pin DSI Cable: [here](https://www.amazon.com/dp/B0CXPJ7F6H)
  - I found that anything shorter will not reach adequately from the Touch screen housing into the electronics compartment. Additionally, the Raspberry Pi 5 uses a different DSI pinout than the `PI TFT43` touchscreen. So while it does come with a DSI cable, it is both too short and uses the incorrect pinout.

- MicroUSB Cable
  - This is needed to connect the Pi to the Microcontroller. Mine is 24” long, but 12” would probably be sufficient. 

- USB C Cable
  - This is only needed temporarily to power the Raspberry Pi for testing and configuration before you connect it to your power supply. I powered mine with my Macbook Pro charger.

- M2.5 & M3 Screws & Nuts
  - The Raspberry Pi holes are M2.5 and the Buck Converter holes are M3. I used M2.5x10mm for the Raspberry Pi and M3x10 for the Buck Converter which allowed me to thread nuts on the other side of the adapter.

- 16AWG Wiring: [here](https://www.amazon.com/gp/product/B0B9J91SJ8)
  - Used for wiring the Buck Converter with the Ender 6's power supply

- 16-14AWG Spade Terminals: [here](https://www.amazon.com/gp/product/B000BW0YUS)
  - Used for connecting the 16AWG Wiring to the Ender 6's Power Supply

- 20-22 Dupont Wires: [here](https://www.amazon.com/IWISS-1550PCS-Connector-Headers-Balancer/dp/B08X6C7PZM/)
  - Used for connecting the Buck Converter to the Raspberry Pi. 

- Optional: USB Wireless Adapater: [here](https://www.amazon.com/dp/B07V4R3QHW)
  - Since the Rasberry Pi will be housed inside the electronics compartment, it will have poor wireless reception. You can optionally connect a USB wireless adapter to it to improve the signal by mounting it ouside of the printer.
  - Be sure to research the Linux driver support of the adapter you purchase. I first selected a [NETGEAR Nighthawk (A8000) - AXE3000](https://www.amazon.com/gp/product/B0B94R78N7) adapter because it seemed like the best on the market. However, I encountered some annoying issue with the `mt7921u` driver as documented: [here](https://github.com/mainsail-crew/crowsnest/issues/276).
  
- *Optional:* Noctua NF-A4x10 Fan: [here](https://www.amazon.com/dp/B07DXS86G7)
  - You can optionally install a Noctua fan alongside the Raspberry Pi and Buck Converter on the gantry rails to keep things cool.

- *Optional*: M3 T Nuts
  - Used to mount the Pi/Buck Converter part on the rails in the electronics compartment. You could print these instead.

- Optional: USB Power Blocker: https://www.amazon.com/PortaPow-Cased-Power-Blocker-Single/dp/B094FYL9QT
  - When you power the printer and the raspberry Pi separately (Like via a USB-C cable for testing), the microcontroller will also receive power from the Pi through the USB cable. Using a power blocker on the Raspberry Pi can help prevent damage to your printer's microcontroller.

<hr>

### Step 2 - Flashing the Printer’s Microcontroller

1. Clone the [Klipper repo](https://github.com/Klipper3d/klipper) and change into the directory.

2. Run make menuconfig and provide the following Configuration
   <img src="https://i.imgur.com/jxorWUx.png" alt="https://i.imgur.com/jxorWUx.png" style="zoom:25%;" />
3. Save the configuration and run `make` to build the binary. 
4. Copy `./out/klipper.bin` to an SD Card, insert it into your Ender 6, and power on the printer. When the device detects the binary file it will automatically flash it to the motherboard. The screen will not indicate that this is happening, however, and it will appear frozen. Wait a minute or two for it to finish and then power the printer back off. At this point in time, your printer will no longer function until you finish this guide or flash an [original firmware](https://www.creality.com/pages/download-ender-6) back onto it. 

<hr>

### Step 3 - Setting up Klipper on the Raspberry Pi

1. Download [Raspberry Pi Imager](https://www.raspberrypi.com/software/) and flash `Raspberry Pi OS Lite (64-bit)` to your MicroSD Card. Before flashing, make sure you configure the installer to connect to your wireless network and enable SSH. 

2. Insert the Micro SD Card into the Raspberry Pi and power it on with a USB-C cable. Wait a minute and SSH into the device. 

3. Run `apt update` to update Debian’s package repositories and then install `git` and any other tools you like such as vim, lm-sensors, etc. 

4. Install Klipper, Moonraker, Mainsail/Fluidd, and KlipperScreen using whichever method you prefer. Normally I would do all of this with Docker but it seems like the current community method is with a shell based installer called [KIAUH](https://github.com/dw-0/kiauh).

5. I also installed Crowsnest since I have a webcam as well as Mobileraker so I can interact with Klipper from my phone. If you’ve done everything correctly, KIAUH will have littered your home directory with directories and everything should be automatically configured for you.

   <img src="https://i.imgur.com/VolMNzc.png" alt="https://i.imgur.com/VolMNzc.png" style="zoom: 25%;" align="left" />

6. Copy the contents of [printer-creality-ender6-2020.cfg](https://github.com/Klipper3d/klipper/blob/master/config/printer-creality-ender6-2020.cfg) to `~/printer_data/config/printer.cfg`. If you have a BLTouch installed make sure you edit the file according to the comments in the `stepper_z`, `safe_z_home`, `bltouch`, and `bed_mesh` sections. 

<hr>

### Step 4 - Installing the Replacement Touchscreen

1. Unplug the printer, and wait for the power supply capacitors to discharge. Flip it on it’s side and remove the bottom cover to access the electronics compartment. Finally, unscrew the touch screen housing and remove it from the printer. There are four T15 screws located near the microcontroller and two T8 screws each on the bottom and back of the housing. Remove the original screen from the housing, but keep the rubber gasket in place. 

2. Connect the DSI cable to one of the Raspberry Pi's `CAM/DISP` header and the header on the `PI TFT43` screen. The orientation of the cable matters as there are only pins on one side of the ribbon. On the Raspberry Pi, the pins should face towards the USB ports. On the `PI TFT43`, the pins should face away from the screen.
   <img src="https://i.imgur.com/UHSxvHq.png" alt="https://i.imgur.com/UHSxvHq.png" style="zoom:25%;" align="left"/>

3. Power on the Pi with a USB-C cable and test that the screen works. After a moment, you should see Debian boot and Klipper Screen should load. Adjust the brightness wheel on the `PI TFT43` until you are satisfied and power off the Pi with the power button. The brightness wheel will no longer be accessible once it is installed inside the housing.
   <img src="https://i.imgur.com/9wJBWLI.jpeg" alt="https://i.imgur.com/9wJBWLI.jpeg" style="zoom:25%;" align="left"/>

4. Mount the screen onto the printed adapter, being extra careful not to overtighten the screws as doing so could break the screen. Power on the Pi once more to confirm there is no backlight bleed from the screws being too tight. Afterwards, mount the `PI TFT43` into the screen housing being sure it is oriented correctly. (The brightness scroll wheel should face the top of the housing and be near the SD Card slot) I don’t believe it’s possible to continue using the SD Card reader located inside the touchscreen housing. Some Googling told me the connector is referred to as a `8 wire Molex PicoBlade` and I was unable to find a cable to connect this to the Raspberry Pi. However, if you did manage to puzzle this out, you would probably be able to make it work if you configure Debian to mount the SD Card at your Klipper Virtual SD card path. I left the speaker inside the electronics compartment disconnected for the same reason. You could probably replace this speaker with a USB device if you so desired.
   <img src="https://i.imgur.com/BOZJG0B.png" alt="https://i.imgur.com/BOZJG0B.png" style="zoom:25%;" align="left"/>

5. Put the housing back together and screw it back into place on the printer.

<hr>

### Step 5 - Installing the Raspberry Pi and Buck Converter

1. Connect the Pi to the microcontroller using the MicroUSB cable and, optionally, any other peripherals like a webcam and wireless USB adapter. Make sure you use the blue USB 3.0 ports, if they’re available. If you're using a power blocker, go ahead and power on the Ender 6 and Raspberry Pi. At this point, if you've done everything correctly, Klipper should automatically detect your printer and KlipperScreen should appear on the screen.

2. Power everything back off and disconnect the Raspberry Pi and microcontroller. 

3. Cut the 16AWG wires to length and strip both sides and attach the spade terminals to one end. Attach the terminals to an open positive and negative slot on the power supply. Attach the other end of the wires to the input side of the buck converter. 

   <img src="https://i.imgur.com/BD5eLfS.jpeg" alt="https://i.imgur.com/BD5eLfS.jpeg" style="zoom:25%;" align="left"/>

4. Twist the voltage regular knob on the buck converter to step it down to 5v. 
   <img src="https://i.imgur.com/f4SneAm.jpeg" alt="https://i.imgur.com/f4SneAm.jpeg" style="zoom:25%;" align="left"/>

5. Power the printer back off and connect the 20-22AWG dupont connectors to the output of the buck converter. Optionally use a multimeter to doublecheck the output before connecting it to your Raspberry Pi to avoid releasing any magic smoke.
   <img src="https://i.imgur.com/LM8C8l5.png" alt="https://i.imgur.com/LM8C8l5.png" style="zoom:25%;" align="left"/>

6. Connect the wires to the 5V and Ground GPIO pins on your Raspberry Pi. You'll likely need to consult a [pinout guide](https://vilros.com/pages/raspberry-pi-5-pinout) to identify the correct pins. 
   <img src="https://i.imgur.com/lunMQlj.jpeg" alt="https://i.imgur.com/lunMQlj.jpeg" style="zoom:25%;" align="left"/>

7. Double check everything is connected correctly and power the printer on. The Raspberry Pi should boot up and KlipperScreen should load and Moonsail should connect to your printer. Try and perform an action like Home X/Y/Z to confirm the printer is operational. 
   <img src="https://i.imgur.com/RBXbVXw.png" alt="https://i.imgur.com/RBXbVXw.png" style="zoom:25%;" align="left"/>

8. Power everything back off and attach the T-Nuts to the bottom of the mounting bracket. Secure it against the gantry rail on your printer. Tidy up all of the cabling.
   <img src="https://i.imgur.com/eXHYA8H.jpeg" alt="https://i.imgur.com/eXHYA8H.jpeg" style="zoom:25%;" align="left"/>

9. Optional: Attach the Noctua fan to the T-Nuts and the USB Port of the Raspberry Pi and mount in next to the raspberry pi and buck converter adapter.
   <img src="https://i.imgur.com/mvI22Qy.jpeg" alt="https://i.imgur.com/mvI22Qy.jpeg" style="zoom:25%;" align="left"/>

   <img src="https://i.imgur.com/eC3yDXB.png" alt="https://i.imgur.com/eC3yDXB.png" style="zoom:25%;" align="left"/>

10. Reinstall the bottom cover and flip the printer back upright.

<hr>

### Step 6 - Configuring the Z-Offset and Bed Mesh

1. If everything is installed correctly, when you power on the printer the Raspberry Pi and Pi TFT43 should turn on as well and Klipper should load.

2. Connect to Mainsail/Fludd and warm the bed and hotend to the temperature you most usually print at. I use PETG so I used 235/90. 

3. Once the hotend is at temperature, `retract` the filament by 20mm so that it doesn’t ooze while you level the bed and calculate a bed mesh. 

4. If you use a BLTouch, you first need to calculate your [Z Offset](https://www.klipper3d.org/Probe_Calibrate.html#calibrating-probe-z-offset). Using Klipper’s web console, issue the `PROBE_CALIBRATE` command. Using a [piece of paper](https://www.klipper3d.org/Bed_Level.html#the-paper-test), adjust the offset as you normally would. When you are satisfied with the results, run `SAVE_CONFIG`. Klipper will write the `z_offset` to your `printer.cfg` file for you and restart itself to load the configuration. 

5. Now that you have determined the Z offset, you need to calculate a bed mesh. Under Fluidd, this is in the `tune` section. Under Mainsail, this is under the `heatmap` section. First Home the hotend on all three axes, then calibrate a bed mesh. I’m no expert, but from what I’ve read you want to shoot to have your mesh range be within at least 0.2mm. I personally shoot for 0.1mm, but it is difficult unless your bed plate is milled with high precision.

6. If your range exceeds 0.2mm, review the visualization of the mesh and adjust your bed accordingly. You can control the X/Y/Z position of the hotend using Klipper and perform the paper test at different locations to assist in leveling the bed. 

7. Once you’ve finished leveling the bed, perform steps 4 and 5 again. Repeat 4-6 over and over until your bed mesh is satisfactory. Once it is, save the mesh and add this to your `printer.cfg` to load it automatically:
   ```bash
   [delayed_gcode my_delayed_gcode]
   initial_duration: 1.0
   gcode:
       BED_MESH_PROFILE LOAD="default"
   ```

<hr>

### Step 7 - Configure Macros and Slicer

1. :warning: TODO: Finish

<hr>

### Optional Steps

#### Configure a Webcam

1. If you’re using a webcam, you can find the `device` path by running `ls` against `/dev/v4l/by-id/`. Afterwards, you can use `v4l-ctl -d $DEVICE –list-formats-ext` to determine the `resolution` and `max_fps`. You might be tempted to choose the highest available resolution and fps, but I have found that there are some bandwidth/latency issues at higher resolutions. You’ll have to play around to see what works best. 
   
1. I have a Logitech C920 and the best configuration I could come up with looks like this. I've read that you could get better performance by using `WebRTC` and `camera-streamer`. But these are not supported on the Raspberry Pi 5 because it lacks the required hardware encoders. On my wireless network, this yields a feed at around 15fps.
   ```bash
   [crowsnest]
   log_path: ~/klipper_logs/crowsnest.log
   log_level: verbose
   delete_log: false
   no_proxy: false
   
   [cam 1]
   mode: ustreamer
   port: 8080
   device: /dev/v4l/by-id/usb-046d_HD_Pro_Webcam_C920_809E7D1F-video-index0
   resolution: 800x600
   max_fps: 30
   v4l2ctl: focus_automatic_continuous=0,focus_absolute=70,brightness=100,contrast=100,saturation=100,sharpness=200,backlight_compensation=1
   custom_flags: --format=yuyv
   ```
   
1. After you’ve configured the webcam profile, go into Mainsail/Fluidd’s settings and add a webcam. Here are my settings: 
   <img src="https://i.imgur.com/6QNwTIN.png" alt="https://i.imgur.com/6QNwTIN.png" style="zoom:25%;" />

#### Configure a USB Wireless Adapter

I noticed that Linux was randomly choosing between `wlan0` and `wlan1` on boot after I installed my USB Wireless Adapter. To force it to use `wlan1`, the USB WIreless Adapter, you can simply add `interface-name=wlan1` to the `[connection]` section in the `/etc/NetworkManager/system-connections/preconfigured.nmconnection` file that Raspberry Pi OS automatically created for you.

```bash
[connection]
interface-name=wlan1
```

Disabling Power Management can also allegedly help improve performance. This is accomplished by adding `wifi.powersave = 2` to the `[connection]` block as well. However, I noticed that when I added this to the `preconfigured.nmconnection` file, `iw wlan1 get power_save` showed that it was still on. So instead I created a new `/etc/NetworkManager/conf.d/wifi-powersave.conf` that simply contained the following.
```bash
[connection]
wifi.powersave = 2
```

#### Print a Klipper Benchy

:warning: TODO
