# RIOT-ROS2

This project enables ROS2 to run on microcontrollers using the RIOT Operating System.

# Architecture

ROS2 is composed of sevral layers. Some have been modified to be able to run on microcontroller.
Here is a summary of the main difference between the ROS2 stack and the RIOT-ROS2 stack :

| Layers | ROS2 | RIOT-ROS2 |
|-|-|-|
| RCLC (ROS Client Library for C) | API only | implemented, following the API |
| RCLCPP (ROS Client Library for C++) | implemented | only supported on `native` board |
| RCLPY (ROS Client Library for Python) | implemented | not supported |
| RCL (ROS Client Library) | implemented | `timer.c` modified |
| RMW (ROS MiddleWare) | Based on DDS | 2 implementations : `ndn-riot` and `emcute` |
| rosidl | DDS, introspection | only introspection |

# Usage

## Clone the project

```sh
git clone https://github.com/astralien3000/riot-ros2.git --recursive
cd riot-ros2
```

Despite there is a Makefile at the root of the repository, you cannot call `make`.

## Linux example : NDN

First, setup the tap interface :
```sh
./RIOT/dist/tools/tapsetup/tapsetup
```

On a first terminal : 
```sh
(cd examples/talker_c && make PORT=tap0 all term)
```

On a second terminal : 
```sh
(cd examples/listener_c && make PORT=tap1 all term)
```

## Linux example : emcute (MQTT-SN)

On a first terminal, setup the tap interface :
```sh
./RIOT/dist/tools/tapsetup/tapsetup
sudo ip a a fec0:affe::1/64 dev tapbr0
```

Compile and run the broker :
```sh
(cd mosquitto.rsmb/rsmb/src/ && make)
(./mosquitto.rsmb/rsmb/src/broker_mqtts broker.conf)
```

On a second terminal :
```sh
(cd examples/talker_c && make RMW=rmw_mqtt PORT=tap0 MYADDR=fec0:affe::2 all term)
```

On a thidr terminal :
```sh
(cd examples/listener_c && make RMW=rmw_mqtt PORT=tap1 MYADDR=fec0:affe::3 all term)
```