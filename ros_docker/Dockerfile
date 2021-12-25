# Adapted from: https://github.com/Tiryoh/docker-ros2-desktop-vnc
# Originally licensed under Apache 2.0, Copyright 2020 Tiryoh
# Modifications Copyright 2020-2021 Unity Technologies
FROM dorowu/ubuntu-desktop-lxde-vnc:bionic
LABEL maintainer="Unity Robotics <unity-robotics@unity3d.com>"

ENV DEBIAN_FRONTEND noninteractive
ENV DEV_NAME=rosdev
ENV ROS_DISTRO=melodic
ENV GROUP_NAME=ros
ENV ROS_WORKSPACE=catkin_ws

RUN echo "Set disable_coredump false" >> /etc/sudo.conf

RUN apt update
RUN apt install -y dirmngr

RUN sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
RUN apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
RUN apt update
RUN apt install -yq ros-${ROS_DISTRO}-ros-base python-rosdep ros-${ROS_DISTRO}-rviz ros-${ROS_DISTRO}-dwa-local-planner ros-${ROS_DISTRO}-turtlebot3-navigation ros-${ROS_DISTRO}-gmapping git

RUN useradd --create-home --home-dir /home/${DEV_NAME} --shell /bin/bash --user-group --groups adm,sudo ${DEV_NAME} && \
    echo "$DEV_NAME:$DEV_NAME" | chpasswd && \
    echo "$DEV_NAME ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

RUN git clone https://github.com/Unity-Technologies/ROS-TCP-Endpoint /home/${DEV_NAME}/${ROS_WORKSPACE}/src/ros_tcp_endpoint -b v0.6.0

COPY --chown=${DEV_NAME} ./ros_docker/unity_slam_example /home/${DEV_NAME}/${ROS_WORKSPACE}/src/unity_slam_example

WORKDIR /home/${DEV_NAME}/${ROS_WORKSPACE}

RUN echo "ROS_IP: 0.0.0.0" > /home/${DEV_NAME}/${ROS_WORKSPACE}/src/ros_tcp_endpoint/config/params.yaml

RUN rosdep init
RUN rosdep update

RUN /bin/bash -c "source /opt/ros/$ROS_DISTRO/setup.bash ;\
rosdep install --from-paths src --ignore-src -r -y;\
    catkin_make ;"


RUN chmod +x src/ros_tcp_endpoint/src/ros_tcp_endpoint/*.py

ENV TURTLEBOT3_MODEL=waffle_pi
                    
RUN echo "source /home/${DEV_NAME}/${ROS_WORKSPACE}/devel/setup.bash" >> /home/${DEV_NAME}/.bashrc

# Informs the environment that the default user is not root, but instead DEV_NAME
ENV USER ${DEV_NAME}

