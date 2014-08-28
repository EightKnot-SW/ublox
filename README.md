# ublox
The ublox package provides support for [u-blox](http://www.u-blox.com) GPS receivers. Only the _serial_ configuration of the driver is documented here, but TCP communication is also supported by the driver (untested).

The driver was originally written by Johannes Meyer. Changes made later are detailed in the version history below.

## Options

The `ublox_gps` node supports the following parameters:
* `device`: Path to the device in `/dev`. Defaults to `/dev/ttyUSB0`.
* `baudrate`: Bit rate of the serial communication. Defaults to 9600.
* `frame_id`: ROS name prepended to frames produced by the node. Defaults to `gps`.
* `meas_rate`: Period in **ms** beween measurements. Defaults to 250.
* `enable_sbas`: Enable satellite-based augmentation system. Defaults to false.
* `dynamic_model`: See U-blox protocol spec. Defaults to `portable`.
* `fix_mode`: Type of fixes supported: `2d`, `3d` or `both`.
* `dr_limit`: Max time in seconds to use dead reckoning after signal is lost. Defaults to 0. (Untested as of 28/08/2014).

A sample launch file is provided in `ublox_gps.launch`. The two topics to which you should subscribe are `/ublox_gps/fix` and `/ublox_gps/fix_velocity`. The angular component of `fix_velocity` is unused.

# Version history

* **0.0.1**:
  - All topics are now published on a private node handle.
  - Velocities are published as stamped twist messages with covariance. Angular components are unsused.
  - `hAcc`, `vAcc` and `sAcc` are used to generate diagonal covariances.
  - Velocities use the correct convention: X-Y-Z = East-North-Up.
  - 2D **or** 3D fix correspond to `STATUS_FIX` (previously only 3D).
  - `fix` and `fix_velocity` are time-stamped synchronously, using the `iTOW` to check arrival times.
  - Added options for changing the CFG-NAV5 settings (see above).
  - Added support for `diagnostic_updater`.
  - _"received ACK"_ messages are elevated to debug level 2.
  - Corrected issue where baudrate was not set correctly using rosparam.
  - Corrected issue where socket destructors were not called.
* **0.0.0**:
  - Forked from https://github.com/tu-darmstadt-ros-pkg/ublox
  - Updated to use catkin.

## FAQs

1. The ublox_gps node cannot open my device, even though I have correctly specified the path in `/dev` - why? Make sure you are the owner of the device, or a member of `dialout` group.

## Links
Consult the [official protocol spec](http://www.u-blox.com/en/download/documents-a-resources/u-blox-6-gps-modules-resources.html) for details  on packets supported by ublox devices.