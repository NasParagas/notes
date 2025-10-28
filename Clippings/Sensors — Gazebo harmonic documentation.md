---
title: "Sensors — Gazebo harmonic documentation"
source: "https://gazebosim.org/docs/harmonic/sensors/"
author:
published:
created: 2025-08-26
description:
tags:
  - "clippings"
---
## Sensors

**Note:** This tutorial is a continuation from the [Moving the robot tutorial](https://gazebosim.org/docs/harmonic/moving_robot/).

In this tutorial we will learn how to add sensors to our robot and to other models in our world. We will use three different sensors: an IMU sensor, a Contact sensor and a Lidar sensor. We will also learn how to launch multiple tasks with just one file using `gz launch`.

You can find the final world of this tutorial showing all these plugins in use [here](https://github.com/gazebosim/docs/blob/master/harmonic/tutorials/sensors/sensor_tutorial.sdf). You may also find an extensive set of world examples with many possible sensors and actuation capabilities in [`gz-sim/examples/worlds`](https://github.com/gazebosim/gz-sim/tree/gz-sim8/examples/worlds) for individual examples. The full set of sensors can be found in the [`gz-sensors` library](https://github.com/gazebosim/gz-sensors). If using ROS, you can see demo launches and bridging configuration for these examples [here](https://github.com/gazebosim/ros_gz/tree/jazzy/ros_gz_sim_demos/launch).

## Preliminaries

When adding a `plugin` to an SDF file which does not currently contain one, the default plugins are not loaded. Before adding a sensor, make sure to add in a couple of logical defaults to your world so that it is possible to continue to use the GZ GUI:

```xml
<sdf version='1.9'>
  <world name='demo'>
    <plugin
        filename="gz-sim-physics-system"
        name="gz::sim::systems::Physics">
    </plugin>
    <plugin
        filename="gz-sim-scene-broadcaster-system"
        name="gz::sim::systems::SceneBroadcaster">
    </plugin>

    <!-- ... -->
```

## IMU sensor

The inertial measurement unit (IMU) outputs the `orientation` of our robot in quaternions, the `angular_velocity` in the three axes (X, Y, Z), and the `linear_acceleration` in the three axes. Let’s use our [moving\_robot.sdf](https://github.com/gazebosim/docs/blob/master/harmonic/tutorials/moving_robot/moving_robot.sdf) world and modify it. Create a new file `sensor_tutorial.sdf` and add the code from `moving_robot.sdf` to it. To define the `IMU` sensor add this code under the `<world>` tag:

```xml
<plugin filename="gz-sim-imu-system"
        name="gz::sim::systems::Imu">
</plugin>
```

This code defines the `IMU` sensor plugin to be used in our world. Now we can add the `IMU` sensor to our robot as follows:

```xml
<sensor name="imu_sensor" type="imu">
    <always_on>1</always_on>
    <update_rate>1</update_rate>
    <visualize>true</visualize>
    <topic>imu</topic>
</sensor>
```

The sensor is usually added to one of the links of our model; we added it under the `chassis` link.

Let’s describe the tags:

- `<always_on>` if true the sensor will always be updated according to the update rate.
- `<update_rate>` the frequency at which the sensor data is generated.
- `<visualize>` if true the sensor is visualized in the GUI.
- `<topic>` name of the topic on which data is published.

**Note:** Not all the tags are supported for all sensors yet.

### Read data from IMU

To read the data from the `IMU`, run the world in one terminal and press the play button:

`gz sim sensor_tutorial.sdf`

In another terminal, run:

`gz topic -e -t /imu`

The last command listens to the messages sent over the `/imu` topic. The IMU data are `orientation`, `angular_velocity` and `linear_acceleration` as described above. It should look like this:

![Imu_message](https://gazebosim.org/docs/harmonic/_images/imu_msgs.png)

Move your robot forward using the keyboard up key. You should see the sensor values changing.

## Lidar sensor

We don’t want our robot to touch the wall at all because this may cause some damage, so instead of the contact sensor we can use the Lidar. Lidar is an acronym for “light detection and ranging”. This sensor can help us detect obstacles around the robot. We will use it to measure the distance between our robot and the wall.

First let’s create a frame to fix our lidar to. This should be added inside of the `vehicle_blue` `<model>` tag, since the lidar frame is attached to the robot’s `chassis`:

```xml
<frame name="lidar_frame" attached_to='chassis'>
    <pose>0.8 0 0.5 0 0 0</pose>
</frame>
```

Then add this plugin under the `<world>` tag, to be able to use the `lidar` sensor:

```xml
<plugin
      filename="gz-sim-sensors-system"
      name="gz::sim::systems::Sensors">
      <render_engine>ogre2</render_engine>
    </plugin>
```

Under the `chassis` link we can add the `lidar` sensor as follows:

```xml
<sensor name='gpu_lidar' type='gpu_lidar'>"
    <pose relative_to='lidar_frame'>0 0 0 0 0 0</pose>
    <topic>lidar</topic>
    <update_rate>10</update_rate>
    <ray>
        <scan>
            <horizontal>
                <samples>640</samples>
                <resolution>1</resolution>
                <min_angle>-1.396263</min_angle>
                <max_angle>1.396263</max_angle>
            </horizontal>
            <vertical>
                <samples>1</samples>
                <resolution>0.01</resolution>
                <min_angle>0</min_angle>
                <max_angle>0</max_angle>
            </vertical>
        </scan>
        <range>
            <min>0.08</min>
            <max>10.0</max>
            <resolution>0.01</resolution>
        </range>
    </ray>
    <always_on>1</always_on>
    <visualize>true</visualize>
</sensor>
```

- First we defined the `name` and `type` of our sensor, then we defined its `<pose>` relative to the `lidar_frame`.
- In the `<topic>` we define the topic on which the lidar data will be published.
- `<update_rate>` is the frequency at which the `lidar` data is generated, in our case `10 Hz` which is equal to `0.1 sec`.
- Under the `<horizontal>` and `<vertical>` tags we define the properties of the horizontal and vertical laser rays.
- `<samples>` is the number of simulated lidar rays to generate per complete laser sweep cycle.
- `<resolution>`: this number is multiplied by samples to determine the number range data points.
- The `<min_angle>` and `<max_angle>` are the angle range of the generated rays.
- Under the `<range>` we define range properties of each simulated ray
	- `<min>` and `<max>` define the minimum and maximum distance for each lidar ray.
	- The `<resolution>` tag here defines the linear resolution of each lidar ray.
- `<always_on>`: if true the sensor will always be updated according to the `<update_rate>`.
- `<visualize>`: if true the sensor is visualized in the GUI.

Now run the world and press the play button in the bottom-left corner:

`gz sim sensor_tutorial.sdf`

Look at the lidar messages on the `/lidar` topic, specifically the `ranges` data:

`gz topic -e -t /lidar`

The lidar message has the following attributes:

### Avoid the wall

Now as we have the lidar on our robot, we can use the `ranges` data and make our robot avoid hitting the wall. To do that, we’ll write a short C++ program that listens to the sensor data and sends velocity commands to the robot. This program is called a node. We will build a node that subscribes to the `/lidar` topic and reads its data. Have a look at this [tutorial](https://gazebosim.org/api/transport/9.0/messages.html) to learn how to build a `publisher` and a `subscriber` node. You can download the finished node for this demo from [here](https://github.com/gazebosim/docs/blob/master/harmonic/tutorials/sensors/lidar_node.cc).

#### The lidar\_node

```cpp
std::string topic_pub = "/cmd_vel";
gz::transport::Node node;
auto pub = node.Advertise<gz::msgs::Twist>(topic_pub);
```

We declare a `node` which will publish to `cmd_vel` topic. Then advertise our node.

```cpp
void cb(const gz::msgs::LaserScan &_msg)
{
  gz::msgs::Twist data;

  bool allMore = true;
  for (int i = 0; i < _msg.ranges_size(); i++)
  {
    if (_msg.ranges(i) < 1.0)
    {
      allMore = false;
      break;
    }
  }
  if (allMore) //if all bigger than one
  {
    data.mutable_linear()->set_x(0.5);
    data.mutable_angular()->set_z(0.0);
  }
  else
  {
    data.mutable_linear()->set_x(0.0);
    data.mutable_angular()->set_z(0.5);
  }
  pub.Publish(data);
}
```

Inside the callback function we check if the range of all rays are bigger than `1.0`. If so we publish a message to our car to move forward. Otherwise we make the robot rotate.

Inside the main we subscribe to the `lidar` topic, and wait until the node is shut down.

#### Build the node

Download the [CMakeLists.txt](https://github.com/gazebosim/docs/blob/master/harmonic/tutorials/sensors/CMakeLists.txt), and in the same folder of `lidar_node` create `build/` directory:

```sh
mkdir build
cd build
```

Run cmake and build the code:

```sh
cmake ..
make lidar_node
```

##### Run the node

Run the node from terminal 1:

```sh
./build/lidar_node
```

Run the world from terminal 2:

```sh
gz sim sensor_tutorial.sdf
```

Now you can see the robot move forward and as it approaches the wall it starts to turn left until it’s clear and moves forward again (be sure to press the play button in the bottom-left corner to make the robot start moving).

## Gazebo launch

Instead of running two different tasks from two different terminals we can make a launch file which will run the `sensor_world` and the `lidar_node` at the same time. Open your text editor and add the following code.

```xml
<?xml version='1.0'?>
<gz version='1.0'>
  <executable name='sensor-world'>
    <command>gz sim sensor_tutorial.sdf</command>
  </executable>

  <executable name='lidar_node'>
    <command>./build/lidar_node</command>
  </executable>

</gz>
```

The launch file is an XML file. We simply define what commands will run under the `<executable>` tag. The first command is `gz sim sensor_tutorial.sdf` which launches the world. And the second command is `./build/lidar_node` which runs the `lidar_node`. Save the file as `sensor_launch.gzlaunch`, and then run it using the following command:

```sh
gz launch sensor_launch.gzlaunch
```

Press the play button to start the simulation. Hurray! Our robot is now moving and avoiding the wall.

To add even more complexity to your simulation, learn how to add actors to a world in the [next tutorial](https://gazebosim.org/docs/harmonic/actors/).

## Video walk-through

A video walk-through of this tutorial is available from our YouTube channel: [Gazebo tutorials: Sensors](https://youtu.be/WcFyGPEfhHc)

![](https://www.youtube.com/watch?v=WcFyGPEfhHc)