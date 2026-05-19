# SROS2 Hardening

Enable ROS2 security and test whether the previous unauthorized `/cmd_vel` attacker can still control the simulator.

## Baseline

Before SROS2, the attacker node could:

- publish unauthorized `Twist` messages to `/turtle1/cmd_vel`
- move the turtle
- subscribe to `/turtle1/pose`
- read turtle position/state data

### Install SROS2 Tools

```bash
sudo apt update
sudo apt install ros-humble-sros2 -y
```

### Check Command Exists

```bash
ros2 security --help
```

![alt text](image.png)


### Create Keystore

```bash
ros2 security create_keystore demo_keystore
```


![alt text](image-1.png)

### Set Environment Variables

```bash
export ROS_SECURITY_KEYSTORE=$HOME/projects/ros2-security-lab/04_sros2_hardening/demo_keystore
```

### Create Security Enclaves

An enclave is like a security identity/context for a ROS2 process. ROS2’s security design uses enclaves to store runtime security artifacts for secure processes.

Create enclaves for legitimate nodes:

```bash
ros2 security create_enclave demo_keystore /turtlesim
ros2 security create_enclave demo_keystore /teleop_turtle
```

![alt text](image-2.png)

### Enable Security Environment

These environment variables should be set everywhere you run protected nodes:

```bash
export ROS_SECURITY_KEYSTORE=$HOME/projects/ros2-security-lab/04_sros2_hardening/demo_keystore
export ROS_SECURITY_ENABLE=true
export ROS_SECURITY_STRATEGY=enforce
```

- `ROS_SECURITY_KEYSTORE`: where security files live
- `ROS_SECURITY_ENABLE=true`: turn security on
- `ROS_SECURITY_STRATEGY=enforce`: fail if security config is missing/bad

### Run Secure Turtlesim

Open Terminal 1:

```bash
source /opt/ros/humble/setup.bash

export ROS_SECURITY_KEYSTORE=$HOME/projects/ros2-security-lab/04_sros2_hardening/demo_keystore
export ROS_SECURITY_ENABLE=true
export ROS_SECURITY_STRATEGY=enforce
export ROS_SECURITY_ENCLAVE=/turtlesim

ros2 run turtlesim turtlesim_node
```

### Run Secure Teleop

Open Terminal 2:

```bash
source /opt/ros/humble/setup.bash

export ROS_SECURITY_KEYSTORE=$HOME/projects/ros2-security-lab/04_sros2_hardening/demo_keystore
export ROS_SECURITY_ENABLE=true
export ROS_SECURITY_STRATEGY=enforce
export ROS_SECURITY_ENCLAVE=/teleop_turtle

ros2 run turtlesim turtle_teleop_key
```










