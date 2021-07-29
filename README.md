# Mini_project
Multiple Drone Formation Using Gazibo ROS and PX4 framework


Gazebo Simulation

Gazebo

(opens new window) is a powerful 3D simulation environment for autonomous robots that is particularly suitable for testing object-avoidance and computer vision. This page describes its use with SITL and a single vehicle. Gazebo can also be used with HITL and for multi-vehicle simulation.

Supported Vehicles: Quad (Iris and Solo, Hex (Typhoon H480), Generic quad delta VTOL, Tailsitter, Plane, Rover, Submarine/UUV.

Installation

Gazebo 9 setup is included in our standard build instructions:

    macOS: Development Environment on Mac
    Linux: Development Environment on Ubuntu LTS / Debian Linux > Gazebo, JMAVSim and NuttX (Pixhawk) Targets
    Windows: Not supported.

Additional installation instructions can be found on gazebosim.org

(opens new window).
#
Running the Simulation

Run a simulation by starting PX4 SITL and gazebo with the airframe configuration to load (multicopters, planes, VTOL, optical flow and multi-vehicle simulations are supported).

The easiest way to do this is to open a terminal in the root directory of the PX4 PX4-Autopilot repository and call make for the desired target. For example, to start a quadrotor simulation (the default):

cd /path/to/PX4-Autopilot
make px4_sitl gazebo

The supported vehicles and make commands are listed below (click links to see vehicle images).

Note

For the full list of build targets run make px4_sitl list_vmd_make_targets (and filter on those that start with gazebo_).
Vehicle 	Command
Quadrotor 	make px4_sitl gazebo
Quadrotor with Optical Flow 	make px4_sitl gazebo_iris_opt_flow
3DR Solo (Quadrotor) 	make px4_sitl gazebo_solo
Typhoon H480 (Hexrotor) (supports video streaming) 	make px4_sitl gazebo_typhoon_h480
Standard Plane 	make px4_sitl gazebo_plane
Standard Plane (with catapult launch) 	make px4_sitl gazebo_plane_catapult
Standard VTOL 	make px4_sitl gazebo_standard_vtol
Tailsitter VTOL 	make px4_sitl gazebo_tailsitter
Ackerman vehicle (UGV/Rover) 	make px4_sitl gazebo_rover
HippoCampus TUHH (UUV: Unmanned Underwater Vehicle) 	make px4_sitl gazebo_uuv_hippocampus
Boat (USV: Unmanned Surface Vehicle) 	make px4_sitl gazebo_boat
Cloudship (Airship)

Usage/Configuration Options

Options that apply to all simulators are covered in the top level Simulation topic (some of these may be duplicated below).
#
Simulating Sensor/Hardware Failure

Simulate Failsafes explains how to trigger safety failsafes like GPS failure and battery drain.

#
Headless Mode

Gazebo can be run in a headless mode in which the Gazebo UI is not launched. This starts up more quickly and uses less system resources (i.e. it is a more "lightweight" way to run the simulation).

Simply prefix the normal make command with HEADLESS=1 as shown:

HEADLESS=1 make px4_sitl gazebo_plane

#
Set Custom Takeoff Location

The takeoff location in SITL Gazebo can be set using environment variables. This will override both the default takeoff location, and any value set for the world.

The variables to set are: PX4_HOME_LAT, PX4_HOME_LON, and PX4_HOME_ALT.

For example:

export PX4_HOME_LAT=28.452386
export PX4_HOME_LON=-13.867138
export PX4_HOME_ALT=28.5
make px4_sitl gazebo

#
Change Simulation Speed

The simulation speed can be increased or decreased with respect to realtime using the environment variable PX4_SIM_SPEED_FACTOR.

export PX4_SIM_SPEED_FACTOR=2
make px4_sitl_default gazebo

For more information see: Simulation > Run Simulation Faster than Realtime.
#
Change Wind Speed

To simulate wind speed, add this plugin to your world file and replace SET_YOUR_WIND_SPEED with the desired speed:

  <plugin name='wind_plugin' filename='libgazebo_wind_plugin.so'>
      <frameId>base_link</frameId>
      <robotNamespace/>
      <xyzOffset>1 0 0</xyzOffset>
      <windDirectionMean>0 1 0</windDirectionMean>
      <windVelocityMean>SET_YOUR_WIND_SPEED</windVelocityMean>
      <windGustDirection>0 0 0</windGustDirection>
      <windGustDuration>0</windGustDuration>
      <windGustStart>0</windGustStart>
      <windGustVelocityMean>0</windGustVelocityMean>
      <windPubTopic>world_wind</windPubTopic>
    </plugin>

You can see this how this is done in PX4/PX4-SITL_gazebo/worlds/windy.world

(opens new window).
#
Using a Joystick

Joystick and thumb-joystick support are supported through QGroundControl (setup instructions here).
#
Improving Distance Sensor Performance

The current default world is PX4/sitl_gazebo/worlds/iris.world

(opens new window)), which uses a heightmap as ground.

This can cause difficulty when using a distance sensor. If there are unexpected results we recommend you change the model in iris.model from uneven_ground to asphalt_plane.

#
Simulating GPS Noise

Gazebo can simulate GPS noise that is similar to that typically found in real systems (otherwise reported GPS values will be noise-free/perfect). This is useful when working on applications that might be impacted by GPS noise - e.g. precision positioning.

GPS noise is enabled if the target vehicle's SDF file contains a value for the gpsNoise element (i.e. it has the line: <gpsNoise>true</gpsNoise>). It is enabled by default in many vehicle SDF files: solo.sdf, iris.sdf, standard_vtol.sdf, delta_wing.sdf, plane.sdf, typhoon_h480, tailsitter.sdf.

To enable/disable GPS noise:

    Build any gazebo target in order to generate SDF files (for all vehicles). For example:

    make px4_sitl gazebo_iris

    TIP

    The SDF files are not overwritten on subsequent builds.

    Open the SDF file for your target vehicle (e.g. ./Tools/sitl_gazebo/models/iris/iris.sdf).

    Search for the gpsNoise element:

    <plugin name='gps_plugin' filename='libgazebo_gps_plugin.so'>
      <robotNamespace/>
      <gpsNoise>true</gpsNoise>
    </plugin>

        If it is present, GPS is enabled. You can disable it by deleting the line: <gpsNoise>true</gpsNoise>
        If it is not preset GPS is disabled. You can enable it by adding the gpsNoise element to the gps_plugin section (as shown above).

The next time you build/restart Gazebo it will use the new GPS noise setting.

#
Loading a Specific World

PX4 supports a number of Gazebo Worlds, which are stored in PX4/sitl_gazebo/worlds
(opens new window)) By default Gazebo displays a flat featureless plane, as defined in empty.world

(opens new window).

You can load any of the worlds by specifying them as the final option in the PX4 configuration target.

For example, to load the warehouse world, you can append it as shown:

make px4_sitl_default gazebo_plane_cam__warehouse

Note

There are two underscores after the model (plane_cam) indicating that the default debugger is used (none). See Building the Code > PX4 Make Build Targets.

You can also specify the full path to a world to load using the PX4_SITL_WORLD environment variable. This is useful if testing a new world that is not yet included with PX4.

TIP

If the loaded world does not align with the map, you may need to set the world location.

#
Set World Location

The vehicle gets spawned very close to the origin of the world model at some simulated GPS location.

Note

The vehicle is not spawned exactly at the Gazebo origin (0,0,0), but using a slight offset, which can highlight a number of common coding issues.

If using a world that recreates a real location (e.g. a particular airport) this can result in a very obvious mismatch between what is displayed in the simulated world, and what is shown on the ground station map. To overcome this problem you can set the location of the world origin to the GPS co-ordinates where it would be in "real life".

Note

You can also set a Custom Takeoff Location that does the same thing. However adding the location to the map is easier (and can still be over-ridden by setting a custom location if needed).

The location of the world is defined in the .world file by specifying the location of the origin using the spherical_coordinates tag. The latitude, longitude, elevation must all be specified (for this to be a valid).

An example can be found in the sonoma_raceway.world

(opens new window):

    <spherical_coordinates>
      <surface_model>EARTH_WGS84</surface_model>
      <latitude_deg>38.161479</latitude_deg>
      <longitude_deg>-122.454630</longitude_deg>
      <elevation>488.0</elevation>
    </spherical_coordinates>
