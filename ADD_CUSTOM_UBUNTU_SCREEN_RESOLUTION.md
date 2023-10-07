To add a custom resolution in Ubuntu 16.04, you can use the `xrandr` command, which allows you to configure display settings. Here's a step-by-step guide:

1. Open a terminal window by pressing `Ctrl+Alt+T` or searching for "Terminal" in the Dash.

2. First, you need to find out the name of your display output. You can do this by running the following command:

   ```
   xrandr
   ```

   Look for the display output you want to set a custom resolution for. It will typically be named something like `VGA-1`, `HDMI-1`, or `DP-1`. Make a note of this name as you'll need it in the next steps.

3. Now, create a modeline for your custom resolution using the `cvt` command. Replace `WIDTH` and `HEIGHT` with the desired resolution values and `REFRESH_RATE` with the desired refresh rate (usually 60Hz):

   ```
   cvt WIDTH HEIGHT REFRESH_RATE
   ```

   For example, if you want a resolution of 1920x1080 at 60Hz, you would run:

   ```
   cvt 1920 1080 60
   ```

   This will output something like:

   ```
   # 1920x1080 59.96 Hz (CVT 2.07M9) hsync: 67.16 kHz; pclk: 173.00 MHz
   Modeline "1920x1080_60.00"  173.00  1920 2048 2248 2576  1080 1083 1088 1120 -hsync +vsync
   ```

4. Copy the entire `Modeline` entry (starting from "1920x1080_60.00" to the end) from the output of the `cvt` command.

5. Next, add the new mode to your display configuration using the `xrandr` command. Replace `DISPLAY_NAME` with the name of your display output from step 2 and paste the `Modeline` entry you copied in the previous step:

   ```
   xrandr --newmode "1920x1080_60.00"  173.00  1920 2048 2248 2576  1080 1083 1088 1120 -hsync +vsync
   ```

6. Now, you need to add the newly created mode to your display output. Run the following command, replacing `DISPLAY_NAME` with your actual display output name and `"1920x1080_60.00"` with the mode name you created:

   ```
   xrandr --addmode DISPLAY_NAME "1920x1080_60.00"
   ```

7. Finally, apply the new resolution to your display:

   ```
   xrandr --output DISPLAY_NAME --mode "1920x1080_60.00"
   ```

Your custom resolution should now be applied. If you are satisfied with the new resolution, you can make it persistent by adding these commands to your startup script or a custom Xorg configuration file.

## Automated Script:

```
#!/bin/bash

# Prompt the user for resolution width
read -p "Enter Resolution Width: " WIDTH

# Prompt the user for resolution height
read -p "Enter Resolution Height: " HEIGHT

# Prompt the user for resolution refresh rate
read -p "Enter Resolution Refresh Rate: " REFRESH_RATE

# Generate a modeline using the user's input
MODELINE=$(cvt $WIDTH $HEIGHT $REFRESH_RATE | grep "Modeline" | sed -e 's/Modeline//')

# Get the display output name
DISPLAY_NAME=$(xrandr | grep " connected" | awk '{print $1}')

# Add the new mode to the display configuration
xrandr --newmode $MODELINE

# Add the new mode to the display output
xrandr --addmode $DISPLAY_NAME ${WIDTH}x${HEIGHT}_${REFRESH_RATE}

# Apply the new resolution
xrandr --output $DISPLAY_NAME --mode ${WIDTH}x${HEIGHT}_${REFRESH_RATE}

echo "Custom resolution set to ${WIDTH}x${HEIGHT} @ ${REFRESH_RATE}Hz."
```
