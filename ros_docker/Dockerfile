FROM dorowu/ubuntu-desktop-lxde-vnc:bionic

ENV ROS_WORKSPACE=/catkin_ws


RUN apt update
RUN apt install -y dirmngr

RUN sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
RUN apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
RUN apt update
RUN apt install -yq ros-melodic-ros-base python-rosdep ros-melodic-rviz ros-melodic-dwa-local-planner ros-melodic-turtlebot3-navigation ros-melodic-gmapping git

RUN git clone https://github.com/Unity-Technologies/ROS-TCP-Endpoint $ROS_WORKSPACE/src/ros_tcp_endpoint -b v0.6.0

COPY ./ros_docker/unity_slam_example $ROS_WORKSPACE/src/unity_slam_example

WORKDIR $ROS_WORKSPACE

RUN echo "ROS_IP: 0.0.0.0" > $ROS_WORKSPACE/src/ros_tcp_endpoint/config/params.yaml

RUN rosdep init
RUN rosdep update

ENV ROS_DISTRO=melodic
RUN /bin/bash -c "source /opt/ros/$ROS_DISTRO/setup.bash ;\
rosdep install --from-paths src --ignore-src -r -y;\
    catkin_make ;"


RUN chmod +x src/ros_tcp_endpoint/src/ros_tcp_endpoint/*.py

ENV TURTLEBOT3_MODEL=waffle_pi
                    
RUN echo ". devel/setup.bash" >> ~/.bashrc

CMD [ "sleep", "infinity" ]