## Build a mini ROS system
This is a hands-on tutorial to build a mini ROS system. ubuntu 16.04 and ROS Kinetic are the default configuration for this tutorial.

---

### create a workspace
#### Step 1: mkdir -p ~/catkin_ws/src
All of the ROS related code you develop throughout this course will reside in your catkin workspace. You only need to create and initialize the workspace once.

First, create the top level catkin workspace directory and a sub-directory named src (pronounced source). The top level directory’s name is arbitrary, but is often called catkin_ws (an abbreviation of catkin_workspace), so we will follow this convention. You can create these two directories with a single command:
```bash
$ mkdir -p ~/catkin_ws/src
```
#### Step 2: catkin_init_workspace
Next, navigate to the src directory with the cd command:
```bash
$ cd ~/catkin_ws/src
```
Now you can initialize the catkin workspace:
```bash
$ catkin_init_workspace
```
#### Step 3: catkin_make
Return to the top level directory,
```bash
$ cd ~/catkin_ws
```
and build the workspace.
**Note**: you must issue this command from within the top level directory (i.e., within ```catkin_ws``` NOT ```catkin_ws/src```)
```bash
$ catkin_make
```

#### Step 4: summary
After the workspace has been created using ```catkin_init_workspace``` and ```catkin_make```, the directory of the project **'Build a mini ROS system'** is shown below:
├── catkin_ws
│   ├── build
│   ├── devel
│   └── src - CMakeLists.txt
└── readme.md

---

### Create a package
#### Step 1: create package
navigate to the src directory:
```bash
$ cd ~/catkin_ws/src
```
The syntax for creating a catkin package is simply,
```bash
$ catkin_create_pkg <your_package_name> [dependency1 dependency2 …]
```
for example,
```bash
$ catkin_create_pkg agent
```
#### Step 2: check the new files
after the package is created, the directory of src is 
├── agent
│   ├── CMakeLists.txt
│   └── package.xml
└── CMakeLists.txt -> /opt/ros/kinetic/share/catkin/cmake/toplevel.cmake

You just created your first catkin package! Navigating inside our newly created package reveals that it contains just two files, ```CMakeLists.txt``` and ```package.xml```. This is a minimum working catkin package. It is not very interesting because it doesn't do anything, but it meets all the requirements for a catkin package. One of the main functions of these two files is to describe dependencies and how catkin should interact with them. We won’t pay much attention to them right now but in future lessons you will see how to modify them.

#### Step 3: typical directory structure for ROS package
In ```/agent```, typical directories and files include:
* scripts (python executables)
* src (C++ source files)
* msg (for custom message definitions)
* srv (for service message definitions)
* include -> headers/libraries that are needed as dependencies
* config -> configuration files
* launch -> provide a more automated way of starting nodes
* package.xml
* CMakeLists.txt
---

### Program and run your first package "agent"
The agent is a one-dimension moving robot. It has one publisher and one subscriber in one node. When it works, it will publish its position to topic /agent/position while subscribe position command from topic /controller/ctrl_cmd.

#### Step 1: program the agent_node
See the code in /src/agent/scripts/agent_node

#### Step 2: change ```agent.py``` to be executable 
```bash
$ chmod u+x agent_node
```

#### Step 3: start the roscore
First change directory to /catkin_ws
```bash
$ cd ~/workspace/Build-a-mini-ROS-system/catkin_ws
```
Then source the setup.bash
```bash
$ source devel/setup.bash
```
Start the roscore
```bash
$ roscore
```

#### Step 4: start the agent_node
First start another terminal, then source the setup.bash
```bash
$ source devel/setup.bash
```
Run the agent_node
```bash
$ rosrun agent agent_node
```

#### Step 5: check the rosnode, rostopic, echo the topic
Open another terminal, then source the setup.bash
```bash
$ source devel/setup.bash
```
First, check all the ros nodes
```bash
$ rostopic list
```
There are two active nodes
```bash
/agent
/rosout
```
Then, check the rostopic
```bash
rostopic 
```
There are three topics, /agent/position is exactly the topic agent_node publishes to.
``` bash
/agent/position
/rosout
/rosout_agg
```
Finally, echo the agent/position topic
```bash
rostopic echo /agent/position
```
You can get the position data published by the agent_node to the /agent/position topic
```bash
data: 0.0
---
data: 0.0
---
data: 0.0
---
data: 0.0
---
data: 0.0
```

---

### Write launch file
It is tedious to open multiple terminals to run nodes. In ROS, we can use launch file to configure the nodes-starting step.

Create a directory ```launch``` in /agent, then create  ```agent.launch``` with the following contents:
```xml
<launch>
  <!-- The agent node -->
  <node name="agent" type="agent_node" pkg="agent"/>
</launch>
```
where
|name|type|pkg|
|--|--|--|
|the name shown in rosnode|the file of the node|the package where the node file resides|
Then change directory to ```/catkin_ws```, all the ROS nodes in the package ```agent``` can be started using the following script
```bash
$ roslaunch agent agent.launch
```

---
### Program your second package "controller"
TODO: This package will generate a service called controller.






---
### Modify the launch file to run multiple packages
Create a directory in /catkin_ws named launch, then add a file ```robot.launch``` to /launch
```xml
<?xml version="1.0"?>
<launch>
    <!-- agent -->
    <include file="$(find agent)/launch/agent.launch" />
</launch>
```
This launch file does no more than executing launch files in each package.
Then change directory to ```/catkin_ws```, the nodes from all packages can be started using:
```bash
$ roslaunch launch/robot.launch
```

---

### Show the compute graph
Open another terminal, source the setup.bash
```bash
$ rqt_graph
```
Then you can have the compute graph for your ROS system.It shows all the nodes and the topics between the nodes.Therefore, it will be easy to figure out the overall picture of the current ROS system.