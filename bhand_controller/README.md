# bhand_controller

This controller for the Barrett Hand is a python node based on the original library pyHand. By using this controller it will be able to set the position or velocity of the motorized joints of the hand and read from all of its sensors (fingertip torque and tactile sensors, temperature) and joint positions.


## Installation

This package depends on the library pcan_python, which is a python wrapper of the C library peak-can. To download and install this library, please visit:  [pcan_python](https://github.com/RobotnikAutomation/pcan_python) and follow the installation instructions.


## 1 bhand_node

ROS wrapper for the pyHand library.

### 1.1 Parameters

   
* ~port (string, default:  '/dev/pcan32'): CAN port 
* ~desired_freq (double, default =  100): control loop frequency. Recommended value: 250Hz
* ~tactile_sensors (bool, default: True): true if the hand has the tactile sensors array.
* ~ft_sensor (bool, default: False): true if the hand has the FT sensor.
*  ~joint_ids (string[], default: [ 'F1', 'F1_TIP', 'F2', 'F2_TIP', 'F3', 'F3_TIP', 'SPREAD_1', 'SPREAD_2']): ids to identify the joints internally. This param should not be modified.
* ~joint_names (string[], default: ['bh_j12_joint', 'bh_j13_joint', 'bh_j22_joint', 'bh_j23_joint', 'bh_j32_joint', 'bh_j31_joint', 'bh_j11_joint', 'bh_j21_joint']): joint names of all the hand joints.
* ~ft_sensor_frame_id (string, default: 'bhand_ft_sensor_link'): frame id for the ft sensor.
   
### 1.2 Subscribed Topics

* ~command (sensor_msgs/JointState)
  Receives the commands to move the hand
  Sets the position/velocity of the hand joints depending on the current control mode.
  Joints available to set positions/velocities:
    * bh_j32_joint -> F3-Base
    * bh_j11_joint -> F1-Spread
    * bh_j12_joint -> F1-Base
    * bh_j21_joint -> F2-Spread
    * bh_j22_joint -> F2-Base
    * bh_j11_joint and bh_j21_joint set the same motor (you can publish on both)
    Example:
    
    ```
    rostopic pub /bhand_node/command sensor_msgs/JointState 
    "header:
    seq: 0
    stamp: {secs: 0, nsecs: 0}
    frame_id: ''
    name: ['bh_j11_joint', 'bh_j32_joint', 'bh_j12_joint', 'bh_j22_joint']
    position: [0 , 0, 0, 0]
    velocity: [0]
    effort: [0]"
    ```
  
  

### 1.3 Published Topics

 * ~joint_states (sensor_msgs/JointState)
   Publishes the current joint states
  
 * ~state(bhand_controller/State)
   Current state of the hand.
   Example:
   
   ```
   state: 300
   control_mode: VELOCITY
   hand_initialized: True
   desired_freq: 250.0
   real_freq: 246.000228882
   state_description: READY_STATE
   temp_f1: [37.0, 58.0]
    temp_f2: [36.0, 60.0]
   temp_f3: [35.0, 54.0]
   temp_spread: [30.0, 62.0]
   ```
 
 * ~tact_array(bhand_controller/TactArray)
   Values from the Barrett Hand tactile array. 
   Units: N/cm²
   Example:
   
   ```
    header: 
    seq: 15277
    stamp: 
     secs: 1408685936
     nsecs: 928946018
    frame_id: ''
    finger1: [0.09000000357627869, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0,   0.019999999552965164, 0.009999999776482582, 0.0, 0.009999999776482582, 0.0, 0.0, 0.019999999552965164, 0.0, 0.03999999910593033, 0.0]
    finger2: [0.0, 0.019999999552965164, 0.0, 0.03999999910593033, 0.029999999329447746, 0.029999999329447746, 0.019999999552965164, 0.019999999552965164, 0.029999999329447746, 0.019999999552965164, 0.019999999552965164, 0.019999999552965164, 0.029999999329447746, 0.029999999329447746, 0.07000000029802322, 0.09000000357627869, 0.0, 0.09000000357627869, 0.009999999776482582, 0.019999999552965164, 0.05999999865889549, 0.009999999776482582, 0.0, 0.03999999910593033]
    finger3: [0.0, 0.0, 0.0, 0.0, 0.019999999552965164, 0.029999999329447746, 0.0, 0.0, 0.019999999552965164, 0.0, 0.009999999776482582, 0.029999999329447746, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.019999999552965164, 0.0, 0.0, 0.0]
    palm: [0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]
    ```
  
 
 *  ~force_torque(bhand_controller/ForceTorque)
   Values from the Barrett Hand Force Torque sensor
   
 
 
### 1.4 Services

* ~actions (bhand_controller/Actions)
  Performs specific actions and movements
  Available actions:
    * INIT_HAND = 1
    * CLOSE_GRASP = 2
    * OPEN_GRASP = 3
    * SET_GRASP_1 = 4
    * SET_GRASP_2 = 5
    * CLOSE_HALF_GRASP = 6
    * TARE_FTS = 7
  Example:
  
    ```
    rosservice call /bhand_node/actions "action: 1"  (Inits the hand)
    ```
  
* ~set_control_mode(std_msgs/String)
  Sets the hand control mode  
  Example:
  
    ```
    rosservice call /bhand_node/set_control_mode "mode: 'VELOCITY'"
    ```

### 1.5 Services Called

None

### 1.6 Action server

None

### 1.7 Action clients called

None

### 1.8 Required tf Transforms

None

### 1.9 Provided tf Transforms

None

### 1.10 Bringup

To run the controller.
```
roslaunch bhand_controller bhand_controller.launch
```

Before any action the hand needs to be initialized calling the service ~actions with the value for INIT_HAND.
