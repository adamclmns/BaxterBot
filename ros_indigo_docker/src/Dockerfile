# Adapted from https://github.com/eruffaldi/linuxsetup/blob/master/dockerbaxter/Dockerfile 
# Referencing - https://github.com/rogaha/docker-desktop/blob/master/Dockerfile For X11 Support

FROM ubuntu:14.04

RUN apt-get -y update
RUN apt-get -y install wget
RUN sh -c 'echo "deb http://packages.ros.org/ros/ubuntu trusty main" > /etc/apt/sources.list.d/ros-latest.list'
RUN wget http://packages.ros.org/ros.key -O - | apt-key add -
RUN apt-get -y update
RUN apt-get -y install ros-indigo-desktop-full
RUN apt-get -y install git-core python-argparse python-wstool python-vcstools python-rosdep ros-indigo-control-msgs ros-indigo-joystick-drivers
RUN apt-get -y install gazebo2 ros-indigo-qt-build ros-indigo-driver-common ros-indigo-gazebo-ros-control ros-indigo-gazebo-ros-pkgs ros-indigo-ros-control ros-indigo-control-toolbox ros-indigo-realtime-tools ros-indigo-ros-controllers ros-indigo-xacro python-wstool ros-indigo-tf-conversions ros-indigo-kdl-parser
# Packages from http://sdk.rethinkrobotics.com/wiki/Simulator_Installation
#extra packages for SSSA TAUM
RUN apt-get -y install ros-indigo-openni2-camera ros-indigo-visp ros-indigo-visp-bridge ros-indigo-moveit-core libzmq-dev libx264-dev ros-indigo-moveit-ros-move-group ros-indigo-ecl-geometry
#RUN apt-get -y install xfce4
# This is just for hot-editing of the image and config files
RUN apt-get -y install nano
# final part
RUN rosdep init
RUN rosdep update
RUN apt-get install python-rosinstall
RUN mkdir -p ~/ros_ws/src
RUN /bin/bash -c "source /opt/ros/indigo/setup.bash"
RUN /bin/bash -c "cd ~/ros_ws/src && wstool init ."
RUN /bin/bash -c "cd ~/ros_ws/src && wstool merge https://raw.githubusercontent.com/RethinkRobotics/baxter/master/baxter_sdk.rosinstall"
RUN /bin/bash -c "cd ~/ros_ws/src && wstool merge https://raw.githubusercontent.com/RethinkRobotics/baxter_simulator/master/baxter_simulator.rosinstall"
RUN /bin/bash -c "cd ~/ros_ws/src && wstool update"
RUN /bin/bash -c "cd ~/ros_ws && source /opt/ros/indigo/setup.bash && catkin_make && catkin_make install"
RUN /bin/bash -c "cd ~/ros_ws/ && wget https://github.com/RethinkRobotics/baxter/raw/master/baxter.sh && chmod u+x baxter.sh"
# TODO: Get Baxter Configuration from user during setup.
# TODO: TEST Gazebo Simulator for Baxter
# TODO: Make sure baxter.sh gets put in the right directory on build
RUN echo "source /opt/ros/indigo/setup.bash" >> ~/.bashrc
# TODO: Add an echo here to tell the user what to do ('roscore' or 'rosrun', etc. Maybe even have a tutorial. )
# FOR X11 Setup
# Set the env variable DEBIAN_FRONTEND to noninteractive
ENV DEBIAN_FRONTEND noninteractive

# Installing the environment required: xserver, xdm, flux box, roc-filer and ssh
RUN apt-get install -y xpra rox-filer openssh-server pwgen xserver-xephyr xdm fluxbox xvfb sudo

# Configuring xdm to allow connections from any IP address and ssh to allow X11 Forwarding. 
RUN sed -i 's/DisplayManager.requestPort/!DisplayManager.requestPort/g' /etc/X11/xdm/xdm-config
RUN sed -i '/#any host/c\*' /etc/X11/xdm/Xaccess
RUN ln -s /usr/bin/Xorg /usr/bin/X
RUN echo X11Forwarding yes >> /etc/ssh/ssh_config


# Set locale (fix the locale warnings)
RUN localedef -v -c -i en_US -f UTF-8 en_US.UTF-8 || :

ADD . /src
EXPOSE 22

CMD "/bin/bash"
#MISSING DOWNLOAD of GAZEBO models inside: user/.gazebo/models
#https://bitbucket.org/osrf/gazebo_models/downloads
