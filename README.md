# Run-Multiple-Turtlebot3-Robot-in-Gazebo-Simulator
This repository shows the steps taken to run more than one of the Turtlebot3 on the Gazebo using a teleop.

###### Requirements:
1. Ubuntu version 18.04
2. ROS melodic
3. Install the Turtlebot3 packages.

## (1) Make a package:
```
$ cd ~/catkin_ws/src

$ catkin_create_pkg multiple_turtlebots_sim
```

## (2) Make a folder for writing launch files:
```
$ cd ~/catkin_ws/src/multiple_turtlebots_sim

$ mkdir launch
```

## (3) Make a launch file for spawning one robot:
```
$ cd ~/catkin_ws/src/multiple_turtlebots_sim/launch

$ gedit one_robot.launch
```

## (3-1) Paste the code below: 
```
<launch>
    <!-- Set arguments -->
    <arg name="init_pose"/>
    <arg name="robot_name"/>

    <!-- Load robot description -->
    <param name="robot_description" command="$(find xacro)/xacro --inorder $(find turtlebot3_description)/urdf/turtlebot3_waffle.urdf.xacro" />

    <!-- Spwan a robot into Gazebo -->
    <node name="spawn_urdf" pkg="gazebo_ros" type="spawn_model"
          args="$(arg init_pose) -urdf -param robot_description -model $(arg robot_name)"
          respawn="false" output="screen"/>
        
    <!-- Publich robot state -->
    <node pkg="robot_state_publisher" type="robot_state_publisher"
          name="robot_state_publisher" output="screen"/>
</launch>
```

## (4) Make a launch file for spawning multiple robots:
```
$ cd ~/catkin_ws/src/multiple_turtlebots_sim/launch

$ gedit robots.launch
```
## (4-1) Paste the code below: 
```
<launch>

    <!-- BEGIN ROBOT 1 -->
    <group ns="robot1">
        <param name="tf_prefix" value="robot1_tf"/>
        <include file="$(find multiple_turtlebots_sim)/launch/one_robot.launch">
            <arg name="init_pose" value="-x 2 -y 0 -z 0 -Y 3.14"/>
            <arg name="robot_name" value="Robot1"/>
        </include>
    </group>

    <!-- BEGIN ROBOT 2 -->
    <group ns="robot2">
        <param name="tf_prefix" value="robot2_tf"/>
        <include file="$(find multiple_turtlebots_sim)/launch/one_robot.launch">
            <arg name="init_pose" value="-x -2 -y 0 -z 0 -Y 0"/>
            <arg name="robot_name" value="Robot2"/>
        </include>
    </group>

    <!-- BEGIN ROBOT 3 -->
    <group ns="robot3">
        <param name="tf_prefix" value="robot3_tf"/>
        <include file="$(find multiple_turtlebots_sim)/launch/one_robot.launch">
            <arg name="init_pose" value="-x 0 -y 2 -z 0 -Y 3.14"/>
            <arg name="robot_name" value="Robot3"/>
        </include>
    </group>

</launch>
```
## (5) Make a launch file for loading a Gazebo world:
```
$ cd ~/catkin_ws/src/multiple_turtlebots_sim/launch

$ gedit simulation.launch
```

## (5-1) Paste the code below:
```
<launch>

    <include file="$(find gazebo_ros)/launch/empty_world.launch">
        <arg name="world_name" value="$(find turtlebot3_gazebo)/worlds/turtlebot3_world.world"/>
        <arg name="paused" value="false"/>
        <arg name="use_sim_time" value="true"/>
        <arg name="gui" value="true"/>
        <arg name="headless" value="false"/>
        <arg name="debug" value="false"/>
    </include>

    <include file="$(find multiple_turtlebots_sim)/launch/robots.launch"/>

</launch>
```

## (6) Update the modified content: 
```
$ cd ~/catkin_ws && catkin_make

$ rospack profile
```

## (7) Now launch turtlebots simulation:
```
$ roslaunch multiple_turtlebots_sim simulation.launch
```

## (8) Run Teleoperation:
Open another terminal and write: 
1. For robot1
```
rosrun teleop_twist_keyboard teleop_twist_keyboard.py /cmd_vel:=/robot1/cmd_vel
```
2. For robot2 
```
rosrun teleop_twist_keyboard teleop_twist_keyboard.py /cmd_vel:=/robot2/cmd_vel
```

## I get an ERROR: [rospack] Error: package 'teleop_twist_keyboard' not found
## I solve it By using this commands:
```
1) cd ~/catkin_ws/src

2) git clone https://github.com/ros-teleop/teleop_twist_keyboard

3) cd ~/catkin_ws

4) catkin_make

5) source ~/catkin_ws/devel/setup.bash

6) source ~/.bashrc

7) gedit ~/.bashrc 

8) source ~/catkin_ws/devel/setup.bash
```


## (9) The Final Result: 
![image](https://user-images.githubusercontent.com/85858256/127796814-d54b284e-cfd0-47c2-89b1-09256aadbee0.png)


![image](https://user-images.githubusercontent.com/85858256/127796840-5dae78a5-9617-4e65-8597-0b9d4ad33010.png)


![image](https://user-images.githubusercontent.com/85858256/127796878-a346e9ba-e986-4e18-996a-558fa1628139.png)
