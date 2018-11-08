# Mapping and Localization {#mapping status=ready}

The Duckiedrone software comes with 5 programs for global localization
outlined in the table below. The two main algorithms are Localization
and SLAM (Simultaneous Localization and Mapping). They both use the
drone's camera to produce a pose estimate, but localization requires a
map of the environment before hand (map.jpg) whereas SLAM builds a map
of the environment as it runs. SLAM is much slower than localization,
and does not run well online even with a fast base station. However it
gives good performance when run offline to create a map, and then
later using that map to localize. The table shows which of the
algorithms are supported under the following conditions: onboard
(running on the raspberry pi), offboard (running on a separate Linux
computer), online (running in real time) and offline (collecting data
first and then computing in retrospect).

|              | Online                | Offline       |
|--------------|-----------------------|---------------|
| **Onboard**  &nbsp; &nbsp; | Localization and SLAM &nbsp; &nbsp; | SLAM  |
| **Offboard** &nbsp; &nbsp; | Localization and SLAM &nbsp; &nbsp; | SLAM  |


The offboard/online mode assumes that the offboard computer is fast
enough to run the programs in real time.  Localization is not
supported offline because localization cannot run in retrospect, only
in real time. Finally, note that SLAM onboard and online is supported,
but runs too slowly to work accurately. Do not expect this program to
produce accurate poses.

The following instructions describe how to use each of the five
supported programs.

These scripts replace the `vision_flow_and_phase.py` script that does
velocity and position control.  The reason is that we save time and
memory by avoiding sending images to different nodes on the Pi and
instead do all processing in a single node.


## Localization - Online

**Onboard:** run `vision_localization_onboard.py` on the pi. You must fly over the area captured in map.jpg. Press `r` in the web interface to toggle localization.

**Offboard:** run `vision_localization_offboard.py` on the pi and `offboard_localization.py` on the remote computer. You must fly over the area captured in map.jpg. Press `r` to toggle localization.


**Change the map:** It is easiest to take photos of the new map with a
  cell phone or other camera. Take them at a height of $25\mbox{cm}$
  and use an image stitching software to generate the map. We
  recommend [auto-stitch](http://matthewalunbrown.com/autostitch/autostitch.html) or [hugin](http://hugin.sourceforge.net/). Replace map.jpg with your new map and change
  the following four parameters in `offboard_localization.py`,
  `onboard_localization.py`, and `localization_helper.py`:
  MAP_PIXEL_WIDTH, MAP_PIXEL_HEIGHT, MAP_REAL_WIDTH,
  MAP_REAL_HEIGHT. You may need to resize the image to be smaller if
  it is too large.

## SLAM

The recommended mode for SLAM is to run it offline; there is not
enough compute even offboard to run it during flight.  It may be
possible to run SLAM online with additional optimization or by
rewriting the algorithm in C++; for this reason we include
instructions for running online as well.

**Offline**: run `vision_localization_onboard.py --SLAM --offline` on
  the Pi or yoru basestation. Press `m` to toggle mapping mode on,
  during which time you can fly to collect data for the map. Pressing
  `m` again will stop the mapping and begin running SLAM offline. It
  is highly recommended that you land the drone for this part. Once it
  tells you that it is finished making the map, press `r` to toggle
  localization over the map you have just made.

**Offboard/online:** run `vision_localization_offboard.py` on the pi. On the offboard computer run `offboard_slam.py`. Press `r` to toggle SLAM.

**Onboard/online:** run `vision_localization_onboard.py --SLAM` on the
  pi. Press `r` to toggle localization.