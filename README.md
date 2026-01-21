# franka-emika-guide


User guide for the Franka Emika robots at TU Delft

For more info: https://frankaemika.github.io/docs/installation_linux.html

## Booking sheet

**Warning** The old google sheet is not used for booking the robots anymore, use the QR codes in the lab. If you are an MSc student, please talk to your daily supervisor before using the robots and booking them.

![Alt text](./Smithy.jpeg?raw=true "Smithy")

# Guide to getting started with the Franka Emika Robots's 

This guide is to help people get up and running with the Franka Emika Panda's for the first time and as a reference for people already familiar with using the Panda's in the lab. 

For someone using the Panda for the first time, the guide should be read sequentially. 

## libfranka

`libfranka` is the software package on the PC connected directly to the Panda controller that supports the communication interface necessary to send torque commands and receive information such as position, velocity, mass matrix etc.

This package should already be installed on the PC's in the lab that are used to run the controllers. **It is not necessary to install this on your own PC**, unless you are running the Panda in the Gazebo environment.  

### Installing libfranka libraray

**On the computers in the lab, this step should be skipped.** The computers should have `libfranka` installed. Don't reinstall it or upgrade/downgrade it, as other students code might rely on this version. 

If you do need to install the `libfranka` library, there is a guide from `Franka Emika`, which is summarised below for convenience. If this guide doesn't work, consult the official guide at, https://frankaemika.github.io/docs/installation_linux.html , as these instructions might become outdated.

- Open a terminal by pressing `ctrl`+`alt`+`t`

- In case you already have some versions of `libfranka` installed, remove them to avoid conflicts with:
``` bash
sudo apt remove "*libfranka*"
sudo apt autoremove
```
For the `Panda`:
Type the following commands to generate and build libfranka

For the `Panda`:
``` bash
cd
sudo apt install build-essential cmake git libpoco-dev libeigen3-dev
git clone --recursive https://github.com/frankaemika/libfranka
cd libfranka
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
cmake --build .
```
For the `fr3`:
``` bash
cd
sudo apt install build-essential cmake git libpoco-dev libeigen3-dev
git clone --recursive https://github.com/frankaemika/libfranka --branch 0.13.3 # only for FR3
cd libfrankaecursive https://github.com/frankaemika/libfranka
cd libfranka
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
cmake --build .
```

This last command may take several minutes to compile the code. Also, the version (in this case 0.13.3) is constantly getting updated by Franka Emika. So check there install instructions (warning, these don't get updated frequently enough and can be behind what is needed) and github for the most up to date instructions and versions of libfranka.

## Setting up the `franka_humun_friendly_controllers`

`franka_ros` is a ROS support package supplied by Franka Emika (https://frankaemika.github.io/docs/franka_ros.html). It contains supporting materials such as CAD files, urdf's and templates for controllers. The human `franka_human_friendly_controllers` package adds more functionality on top of what is given in the base `franka_ros` package, especially features to help researchers that use impedance based control and need human friendly interactions. 

On the controller Desktop computer in the lab, set up a catkin workspace for yourself at `~/Documents/your_name_ws/`, .e.g. `~/Documents/giovanni_ws/`. 

`cd` to your workspace, download and install the `franka_ros` package

``` bash
cd ~/Documents/your_name_ws/
mkdir src
cd src
git clone --recursive https://github.com/frankaemika/franka_ros 
```
Then install the `franka_human_friendly_controllers` within the `franka_ros` folders

``` bash
cd src/franka_ros
git clone https://github.com/franzesegiovanni/franka_human_friendly_controllers.git
```

Then build the packages, sourcing the compatable version of `libranka`, depending the robot model, `panda`, or `fr3`:
### Panda version 
```bash
catkin_make -DMAKE_BUILD_TYPE=Release -DFranka_DIR:PATH=~/libfranka_panda/build
```
or 

```bash
catkin build -DMAKE_BUILD_TYPE=Release -DFranka_DIR:PATH=~/libfranka_panda/build
```
### fr3 robot version 
```bash
catkin_make -DMAKE_BUILD_TYPE=Release -DFranka_DIR:PATH=~/libfranka_fr3/build
```
or 

```bash
catkin build -DMAKE_BUILD_TYPE=Release -DFranka_DIR:PATH=~/libfranka_fr3/build
```

## Running the controllers on the Robot
- Switch on the power switch of the controller
- On the PC, set the ethernet too "connect to panda", i.e. manually set the IP address to 172.16.0.1 
- In a web browser (.e.g.firefox), type in the IP address of the robot you are using, a `Franka Emika` browser application screen should appear
	- **Tip**: If the browser application is not found, try using the ping command from the command line e.g. `ping 172.16.0.2`. There are multiple Panda's in the lab with multiple IP address, this can be quick way to check which on is connected (we probably should write the ip address on each robot!) 
	- **Tip:** Check that both emergency power switches are not engaged (The `fr3`'s only have one, the `Panda` has an extra emergency between stop between the controller and the power socket. 
- In the browser application, unlock the robot joints. 
- In the menu (three bars at the right top corner), activate the `fci`
- In the terminal, source the ROS packages you built
`source ~/Documents/your_name_ws/devel/setup.bash`
- Then, to you run the controller on the robot run:
``` bash
roslaunch franka_human_friendly_controllers <controller_name>.launch robot_ip:=<ROBOT_IP> load_gripper:=True
```
for example, the Cartesian impedance controller with the robot that has an IP address of 172.16.0.2:
``` bash
roslaunch franka_human_friendly_controllers cartesian_variable_impedance_controller.launch robot_ip:=172.16.0.2 load_gripper:=True
```
- If you need to use the gripper, in a different terminal, run (don't forget to source again):
```bash
rosrun franka_human_friendly_controllers franka_gripper_online
```


## Connecting to the Robot with your own computer

1. Connect your PC/laptop to the network that ROS_MASTER is running on .i.e. connect your PC/laptop to the network switch with the ethernet cable
2. Create a new wired network and in IPv4 set Manual and put a new ip for your computer, in Ubuntu settings
<PC/laptop>=A.B.C.F where F is different from the IP address of the computer connected to the robot controller, <controller_computer_ip> (which is ussually 172.16.0.1 in the lab) or the <robot_ip> (which is in the range of 172.16.0.2 - 172.16.0.5, for the four Franka Emika robots we have in the lab). Netmask is the same  ```255.255.255.0```. 
Save the network. 

![Alt text](./wired_network_settings.png?raw=true "Wired_Settings")

3. Add this to your bash file (```gedit ~/.bashrc```): 
```bash
export ROS_MASTER_URI=http://<controller_computer_ip>:11311
export ROS_IP=<PC/laptop>
export ROS_HOSTNAME=<PC/laptop>
```

For example

```bash
export ROS_MASTER_URI=http://172.16.0.1:11311
export ROS_IP=172.16.0.25
export ROS_HOSTNAME=172.16.0.25
```

**Note** to switch the `ROS_MASTER_URI` back to the default settings, add the line 

```bash
export ROS_MASTER_URI=http://localhost:11311
``` 

to the `~/.bashrc` file and comment out the previous export lines. Then re `source` the `~/.bashrc` file

4. source the `~/.bashrc` file

```bash
source ~/.bashrc
```

5. source ros
source ```/opt/ros/<ros_version>/setup.bash```

6. Test the data_streaming with 
```rostopic list``` 

## Running the robot in Gazebo
This section is copied from https://github.com/franzesegiovanni/franka_human_friendly_controllers/, if there is any problems with using the instructions below to use Gazebo, please check there for a more up to date version and let us know of the problem. 

The first time you run it, set up Gazebo with python3 setup_gazebo.py (in the 'franka_human_friendly_controllers' package).

To lunch the cartesian impedance controller in simulation:

```bash
roslaunch franka_gazebo panda.launch x:=-0.5 world:=$(rospack find franka_gazebo)/world/stone.sdf controller:=cartesian_variable_impedance_controller rviz:=true
```

To kill gazebo run: killall -9 gazebo & killall -9 gzserver  & killall -9 gzclient

## Bimanual Setup

## Learning Resources

Some resources to get up to speed with the prequisites for using the Franka Emika robots and ROS in general. A lot of these are hijacked from https://www.ipb.uni-bonn.de/onboarding/. Also, these haven't been tested, so if you do use one of these, please let us know if it was useful or not, or if know of other good resources you have actually used, so we can curate the list over time. 

### ROS

Lecture series:
https://www.youtube.com/playlist?list=PLE-BQwvVGf8HOvwXPgtDfWoxd4Cc6ghiP

Official ROS tutorials:
https://wiki.ros.org/ROS/Tutorials

### Linux/Ubuntu

Recommendations are very welcome for this!!

### Git

A beginners guide:
https://rogerdudler.github.io/git-guide/

More advanced course:
https://missing.csail.mit.edu/2020/version-control/

Interactive tutorial:
https://learngitbranching.js.org/

Another fundamentals tutorial:
https://www.youtube.com/watch?v=HVsySz-h9r4

A very hand guide for when you're in trouble:
https://ohshitgit.com/

### Python

Python Intro from Bonn University:
https://www.youtube.com/playlist?list=PLgnQpQtFTOGRKTPxhXZ68kKAQtsy76s0S

### C++

Two courses from Bonn University:
https://www.youtube.com/playlist?list=PLgnQpQtFTOGR50iIOtO36nK6aNPtVq98C
https://www.ipb.uni-bonn.de/cpp-course-2020/
