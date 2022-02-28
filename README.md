# Udacity_Robo_SW_ND_P3_Where_Am_I
Use the Adaptive Monte Carlo Localization algorithm in Ros to localize my robot. it is project 3 for Udacity Robotics Software Engineer Nanodegree Program 


# Project Overview

 Where Am I? localization project! In this project, We will learn to utilize ROS AMCL package to accurately localize a mobile robot inside a map in the Gazebo simulation .

* Create a ROS package that launches a custom robot model in a custom Gazebo world
* Utilize the ROS AMCL package and the Tele-Operation / Navigation Stack to localize the robot
* Explore, add, and tune specific parameters corresponding to each package to achieve the best possible localization results

## Native Installation & Virtual Machine

If you are working with a native ROS installation or using a VM, some of the following package might need to be installed. You could install them as shown below:

```
$ sudo apt-get install ros-kinetic-navigation
$ sudo apt-get install ros-kinetic-map-server
$ sudo apt-get install ros-kinetic-move-base
$ sudo apt-get install ros-kinetic-amcl
```

## PGM Map File

The map ROS AMCL Package uses is a pgm file. A pgm file is a grayscale image file. For more information about pgm file or more generally, pnm file, please refer to Netpbm format Wiki Page.

By default, AMCL package will treat 'darker' pixels as obstacle in the pgm map file, and 'lighter' pixels as free space. The threshold could be set as a parameter which we will cover when we are building the launch file.

Navigate to your ROS package folder and create a maps folder. That's where your map file will reside.

```
$ cd /home/workspace/catkin_ws/src/<YOUR PACKAGE NAME>
$ mkdir maps
```

## PGM Map Creator
### Install Dependencies

We need libignition-math2-dev and protobuf-compiler to compile the map creator:

```
sudo apt-get install libignition-math2-dev protobuf-compiler
```
### Clone the Repository

Clone the package pgm_map_creator to your src folder.
```
cd /home/workspace/catkin_ws/src/
git clone https://github.com/hyfan1116/pgm_map_creator
```

### Build the package:
```
cd ..
catkin_make
```

### Add and Edit the World File

Copy the Gazebo world you created to the world folder
```
cp <YOUR GAZEBO WORLD FILE> src/pgm_map_creator/world/<YOUR GAZEBO WORLD FILE>
```

Insert the map creator plugin to your map file Open the map file using the editor of your choice. Add the following tag towards the end of the file, but just before </world> tag:

```
<plugin filename="libcollision_map_creator.so" name="collision_map_creator"/>
```

### Create the PGM Map!

Open a terminal, run gzerver with the map file:

```
gzserver src/pgm_map_creator/world/<YOUR GAZEBO WORLD FILE>
```

Open another terminal, launch the request_publisher node

```
roslaunch pgm_map_creator request_publisher.launch
```

Wait for the plugin to generate map. It will be located in the map folder of the pgm_map_creator! Open it to do a quick check of the map. If the map is cropped, you might want to adjust the parameters in launch/request_publisher.launch, namely the x and y values, which defines the size of the map:

```
  <arg name="xmin" default="-15" />
  <arg name="xmax" default="15" />
  <arg name="ymin" default="-15" />
  <arg name="ymax" default="15" />
  <arg name="scan_height" default="5" />
  <arg name="resolution" default="0.01" />
```

### Edit the Map

Remember, the map is an image, which means you could edit it using image processing softwares like gimp in Linux. If you have found the map not accurate due to the models, feel free to edit the pgm file directly!

### Add the Map to Your Package

Now we have the map file, let us move it to where it is needed! That is the maps folder you created at the very beginning.

```
cd /home/workspace/catkin_ws/
cp src/pgm_map_creator/maps/<YOUR MAP NAME>  src/<YOUR PACKAGE NAME>/maps/<YOUR MAP NAME>
```

You would also need a yaml file providing the metadata about the map. Create a yaml file next to your map:

```
cd src/<YOUR PACKAGE NAME>/src/maps
touch <YOUR MAP NAME>.yaml
```

Open the yaml file and add the following lines to it:

```
image: <YOUR MAP NAME>
resolution: 0.01
origin: [-15.0, -15.0, 0.0]
occupied_thresh: 0.65
free_thresh: 0.196
negate: 0
```

Note that the origin of the map should correspond to your map's size. For example, the default map size is 30 by 30, so the origin will be [-15, -15, 0], i.e. half the size of the map.


## Teleop Package

If you prefer to control your robot to help it localize itself as you did in the lab, you would need to add the teleop node to your package. Thanks to the ROS community, we could use ros-teleop package to send command to the robot using keyboard or controller.

### Clone the ros-teleop package to your src folder:

```
cd /home/workspace/catkin_ws/src
git clone https://github.com/ros-teleop/teleop_twist_keyboard
```

### Build the package and source the setup script:

```
cd ..
catkin_make
source devel/setup.bash
```

### Now you could run the teleop script as is described in the README file:

```
rosrun teleop_twist_keyboard teleop_twist_keyboard.py
```

# Project Directory Structure

        .[RoboND-Where-Am-I-Project-P3]            # Where Am I Project
	├── my_robot                               # my_robot package        
	│   │   ├── config                         # config folder for configuration files   
	│   │   │   ├── base_local_planner_params.yaml
	│   │   │   ├── costmap_common_params.yaml
	│   │   │   ├── global_costmap_params.yaml
	│   │   │   ├── local_costmap_params.yaml
	│   │   ├── launch                         # launch folder for launch files   
	│   │   │   ├── amcl.launch
	│   │   │   ├── robot_description.launch
	│   │   │   ├── world.launch
	│   │   ├── maps                           	   # maps folder for maps
	│   │   │   ├── map.pgm
	│   │   │   ├── map.yaml
	│   │   ├── meshes                         # meshes folder for sensors
	│   │   │   ├── hokuyo.dae
	│   │   ├── rviz                           # rviz folder for rviz configuration files
	│   │   │   ├── default.rviz
	│   │   ├── urdf                           # urdf folder for xarco files
	│   │   │   ├── my_robot.gazebo
	│   │   │   ├── my_robot.xacro
	│   │   ├── worlds                         # world folder for world files
	│   │   │   ├── empty.world
	│   │   │   ├── myworld.world
	│   │   ├── CMakeLists.txt                 # compiler instructions
	│   │   ├── package.xml                    # package info
	│   ├── pgm_map_creator                    # pgm_map_creator        
	│   │   ├── launch                         # launch folder for launch files   
	│   │   │   ├── request_publisher.launch
	│   │   ├── maps                           # maps folder for generated maps
	│   │   │   ├── Backup_map.pgm
	│   │   │   ├── map.pgm
	│   │   ├── msgs                           # msgs folder for communication files
	│   │   │   ├── CMakeLists.txt
	│   │   │   ├── collision_map_request.proto
	│   │   ├── src                            # src folder for main function
	│   │   │   ├── collision_map_creator.cc
	│   │   │   ├── request_publisher.cc
	│   │   ├── world                          # world folder for world files
	│   │   │   ├── myoffice.world
	│   │   │   ├── udacity_mtv
	│   │   ├── CMakeLists.txt                 # compiler instructions
	│   │   ├── LICENSE                        # License for repository
	│   │   ├── README.md                      # README for documentation
	│   │   ├── package.xml                    # package info
	│   ├── teleop_twist_keyboard              # teleop_twist_keyboard
	│   │   ├── CHANGELOG.rst                  # change log
	│   │   ├── CMakeLists.txt                 # compiler instructions
	│   │   ├── README.md                      # README for documentation
	│   │   ├── package.xml                    # package info
	│   │   ├── teleop_twist_keyboard.py       # keyboard controller
                         

## Create the my_robot Package

### create and initialize a catkin_ws Feel free to skip if you already have a catkin_ws.

```
$ mkdir -p /home/workspace/catkin_ws/src
$ cd /home/workspace/catkin_ws/src
$ catkin_init_workspace
```

### Clone or Download This Project Under the /home/workspace/catkin_ws/src

```
$ cd /home/workspace/catkin_ws/src
$ git clone https://github.com/umtclskn/RoboND-Where-Am-I-Project-P3
```

### Build Package

Now that you’ve included specific instructions for your process_image.cpp code in CMakeLists.txt, compile it with:

```
$ cd /home/workspace/catkin_ws/
$ catkin_make
```

## AMCL Package

You learned about Monte Carlo Localization (MCL) in great detail in the previous lessons. Adaptive Monte Carlo Localization (AMCL) dynamically adjusts the number of particles over a period of time, as the robot navigates around in a map. This adaptive process offers a significant computational advantage over MCL.

The ROS AMCL package (http://wiki.ros.org/amcl) implements this variant and you will integrate this package with your robot to localize it inside the provided map.
Launching

You have your robot, your map, your localization and navigation nodes. Let’s launch it all and test it!

### First, launch the simulation:

```
$ cd /home/workspace/catkin_ws/
$ roslaunch <YOUR PACKAGE NAME> <YOUR WORLD>.launch
```

### In a new terminal, launch the amcl launch file:

```
$ roslaunch <YOUR PACKAGE NAME> amcl.launch
```

## Rviz Configuration

As you did in a previous section, setup your RViz by adding the necessary displays and selecting the required topics to visualize the robot and also the map.
Add by display type

### In Rviz,
   
   * Select odom for fixed frame
   * Click the “Add” button and
        * add RobotModel: this would add the robot itself to RViz
        * add Map and select first topic/map: the second and third topics in the list will show the global costmap, and the local costmap. Both can be helpful to tune your parameters
        * add PoseArray and select topic/particlecloud: this will display a set of arrows around the robot
   
