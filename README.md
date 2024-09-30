# Ender 6 Klipper Guide

## Summary

After about a week of work and multiple parts orders and prints I just finished converting my Ender 6 to use Klipper. I found that a lot of the information available online is out of date, so I thought I'd write up a little guide so that others might have an easier time than myself in the future. Apologies ahead of time if any of this isn’t super clear, I mostly wrote it after working my way through the process and realizing I should document it for the community. Feel free to leave comments about the different ways you’ve set up Klipper on your Ender 6 as well.

I have a BL Touch installed and am using the original touch screen housing, but have installed an aftermarket screen so that the latest version of Klipper can be used instead of [Desuuuu's outdated fork](https://github.com/Desuuuu/klipper).

:warning: TODO: Photo of printer with Benchy

<hr>

## Instructions

### Part 1 (Printing and Purchasing):

Before you flash anything to your printer or take anything apart, you need to use it to print a few parts. I used PETG and printed at 235/90 and 30mm/s personally, which delivered great results. 

**Parts you need to print:**

- Raspberry Pi & Buck Converter Mount: [here](https://www.thingiverse.com/thing:6273981)
  - This part will allow you to mount your Raspberry Pi below the printer in the electronics compartment and allow you to power it with the printer’s power supply. This model does not enclose the buck converter, so it is not ideal as you can be shocked by it. So be careful and use common sense when handling it. You may wish to print with supports so that the SD card cutout can be bridged properly.

- PIT TFT43 Screen Adapter: [here](https://www.thingiverse.com/thing:5246155)
  - Desuuuu’s fork of Klipper that made the default touchscreen for the Ender 6 function was never merged upstream, so it does not work with Klipper. In order to continue having a touchscreen and use the latest version of Klipper, you have to replace it with one that is compatible. The `PI TFT43` just so happens to fit almost perfectly, but does require an adapter.
- 3x M3 T-Nuts: [here](https://www.thingiverse.com/thing:3050607)
  - These are only needed if you wish to save a little bit of money and avoid purchasing them in bulk. But you need a way to mount the Raspberry Pi/Buck Converter part on the rails in your electronics compartment. 

**Parts you need to have/buy (~$200):**

- Wifi Capable Raspberry Pi: [here](https://www.amazon.com/dp/B0CPWH8FL9)
  - I bought the 5, you can probably get away with something cheaper if you wish. [Here](https://i.imgur.com/tADPJH6.png) is a screenshot from htop during a print which shows it’s massively overpowered for what is required.
- BIGTREETECH PI TFT43 Screen: [here](https://www.amazon.com/dp/B09791ZG1B)
- MicroSD Card: [here](https://www.amazon.com/SanDisk-128GB-microSDXC-Memory-Adapter/dp/B0B7NTY2S6/)
- Buck Converter: [here](https://www.amazon.com/dp/B07WQJ2GD6)
- 50CM 22Pin -> 15Pin DSI Cable: [here](https://www.amazon.com/dp/B0CXPJ7F6H)
  - I found that anything shorter will not reach adequately from the Touch screen housing into the electronics compartment.
- MicroUSB Cable: [here](https://www.amazon.com/SUNGUY-3-Pack-Braided-Charging-Galaxy/dp/B07G934SJ9)
  - This is needed to connect the Pi to the Microcontroller. Mine is 24” long, but 12” would probably be sufficient. 
- USB C Cable: [here](https://www.amazon.com/Anker-Charging-MacBook-Galaxy-Charger/dp/B088NRLMPV)
  - This is only needed temporarily to power the Raspberry Pi before you connect it to your power supply. I didn’t personally use this product, so I can’t guarantee it works. I powered mine with my Macbook Pro charger. 
- 12AWG Wiring: [here](https://www.amazon.com/dp/B0796L85CD)
- 12-10 AWG Ring Terminals: [here](https://www.amazon.com/dp/B09XGQN2NS)
- M2.5 & M3 Screws: [here](https://www.amazon.com/dp/B07HC3LQYS)
  - The Raspberry Pi & Pi TFT43 holes are M2.5 and the Buck Converter holes are M3
- M3 T Nuts: [here](https://www.amazon.com/dp/B07Z4YH6NP)
  - Used to mount the Pi/Buck Converter part on the rails in the electronics compartment

<hr>

### Step 2 (Flashing the Printer’s Microcontroller):

1. Clone the [Klipper repo](https://github.com/Klipper3d/klipper) and change into the directory.
2. Run make menuconfig and provide the following Configuration
   <img src="https://i.imgur.com/jxorWUx.png" alt="https://i.imgur.com/jxorWUx.png" style="zoom:25%;" />
3. Save the configuration and run `make` to build the binary. 
4. Copy `./out/klipper.bin` to an SD Card, insert it into your Ender 6, and power on the printer. When the device detects the binary file it will automatically flash it to the motherboard. The screen will not indicate that this is happening, however, and it will appear frozen. Wait a minute or two for it to finish and then power the printer back off. At this point in time, your printer will no longer function until you finish this guide or flash an [original firmware](https://www.creality.com/pages/download-ender-6) back onto it. 

<hr>

### Step 3 (Setting up Klipper on the Raspberry Pi):

1. Download [Raspberry Pi Imager](https://www.raspberrypi.com/software/) and flash `Raspberry Pi OS Lite (64-bit)` to your MicroSD Card. Before flashing, make sure you configure the installer to connect to your wireless network and enable SSH. 

2. Insert the Micro SD Card into the Raspberry Pi and power it on with a USB-C cable. Wait a minute and SSH into the device. 

3. Run `apt update` to update Debian’s package repositories and then install `git` and any other tools you like such as vim, lm-sensors, etc. 

4. Install Klipper, Moonraker, Mainsail/Fluidd, and KlipperScreen using whichever method you prefer. Normally I would do all of this with Docker but it seems like the current community method is with a shell based installer called [KIAUH](https://github.com/dw-0/kiauh).

5. I also installed Crowsnest since I have a webcam as well as Mobileraker so I can interact with Klipper from my phone. If you’ve done everything correctly, KIAUH will have littered your home directory with directories and everything should be automatically configured for you. Your guess is as good as mine how well all of this will continue to work and update in a year or two.

   <img src="https://i.imgur.com/VolMNzc.png" alt="https://i.imgur.com/VolMNzc.png" style="zoom: 25%;" align="left" />

6. Copy the contents of [printer-creality-ender6-2020.cfg](https://github.com/Klipper3d/klipper/blob/master/config/printer-creality-ender6-2020.cfg) to `~/printer_data/config/printer.cfg`. If you have a BLTouch installed make sure you edit the file according to the comments in the `stepper_z`, `safe_z_home`, `bltouch`, and `bed_mesh` sections. 

7. If you have a webcam, modify `printer_data/config/crowsnest.cfg` accordingly. I have a Logitech C920 and the best configuration I could come up with looks like this: 

   <img src="https://i.imgur.com/n27ahDh.png" alt="https://i.imgur.com/n27ahDh.png" style="zoom:25%;" align="left"/>

<hr>

### Step 4 (Installing the Replacement Touchscreen):

1. Unplug the printer, wait for the power supply capacitors to discharge, and flip it on it’s side.
2. Remove the bottom cover to access the electronics compartment. 
3. I don’t believe it’s possible to continue using the SD Card reader located inside the touchscreen housing. Some Googling told me the connector is referred to as a `8 wire Molex PicoBlade` and I was unable to find a cable to connect this to the Raspberry Pi. However, if you did manage to puzzle this out, you would probably be able to make it work if you configure Debian to mount the SD Card at your Klipper Virtual SD card path?
4. :warning: TODO: Finish

<hr>

### Step 5 (Installing the Raspberry Pi and Buck Converter):

1. Connect the Pi to the microcontroller and, optionally, any other peripherals like a webcam. Make sure you use the blue USB 3.0 ports, if they’re available. 
2. `speaker-test`
3. Reinstall the bottom cover and flip the printer back upright.
4. :warning: TODO: Finish

<hr>

### Step 6: (Configuring the Z-Offset & Bed Mesh):

1. If everything is installed correctly, when you power on the printer the Raspberry Pi and Pi TFT43 should turn on as well and Klipper should load.

2. Connect to Mainsail/Fludd and warm the bed and hotend to the temperature you most usually print at. I use PETG so I used 235/90. 

3. Once the hotend is at temperature, `retract` the filament by 20mm so that it doesn’t ooze while you level the bed and calculate a bed mesh. 

4. If you use a BLTouch, you first need to calculate your [Z Offset](https://www.klipper3d.org/Probe_Calibrate.html#calibrating-probe-z-offset). Using Klipper’s web console, issue the `PROBE_CALIBRATE` command. Using a [piece of paper](https://www.klipper3d.org/Bed_Level.html#the-paper-test), adjust the offset as you normally would. When you are satisfied with the results, run `SAVE_CONFIG`. Klipper will write the `z_offset` to your `printer.cfg` file for you and restart itself to load the configuration. 

5. Now that you have determined the Z offset, you need to calculate a bed mesh. Under Fluidd, this is in the `tune` section. Under Mainsail, this is under the `heatmap` section. First Home the hotend on all three axes, then calibrate a bed mesh. I’m no expert, but from what I’ve read you want to shoot to have your mesh range be within at least 0.2mm. I personally shoot for 0.1mm, but it is difficult unless your bed plate is milled to extremely precise levels. 

6. If your range exceeds 0.2mm, review the visualization of the mesh and adjust your bed accordingly. You can control the X/Y/Z position of the hotend using Klipper and perform the paper test at different locations to assist in leveling the bed. 

7. Once you’ve finished leveling the bed, perform steps 4 and 5. Repeat 4-6 over and over until your bed mesh is satisfactory. Once it is, save the mesh and add this to your `printer.cfg` to load it automatically:
   ```bash
   [delayed_gcode my_delayed_gcode]
   initial_duration: 1.0
   gcode:
       BED_MESH_PROFILE LOAD="default"
   ```

<hr>

### Step Optional: (Configuring a Webcam):

1. If you’re using a webcam, and my Logitech C920 profile didn’t work for you, you can find the `device` path by running `ls` against `/dev/v4l/by-id/`. Afterwards, you can use `v4l-ctl -d $DEVICE –list-formats-ext` to determine the `resolution` and `max_fps`. You might be tempted to choose the highest available resolution and fps, but I have found that there are some bandwidth/latency issues at higher resolutions so I chose 720p@30fps which really only operates at about 10fps. You’ll have to play around to see what works best. After you’ve configured the webcam profile, go into Mainsail/Fluidd’s settings and add a webcam. Here are my settings: 
   <img src="https://i.imgur.com/6QNwTIN.png" alt="https://i.imgur.com/6QNwTIN.png" style="zoom:25%;" />

<hr>

### Step 7: (Configuring Macros & Slicer):

1. :warning: TODO: Finish

<hr>

### Step 8 (Print a Klipper Benchy):

1. :warning: TODO: Show closeup of Benchy on Printer Bed