# Adapted from https://github.com/eruffaldi/linuxsetup/blob/master/dockerbaxter/Dockerfile 
# Referencing - https://github.com/rogaha/docker-desktop/blob/master/Dockerfile For X11 Support
# Also based on https://sdk.rethinkrobotics.com/wiki/Simulator_Installation

FROM ubuntu:14.04

# Must run this or you can't find any sources
RUN apt-get -y update
# These are non-ros things that I like to have handy. May be used during build, modify at own risk.
RUN apt-get -y install wget nano git

# Installing ROS Prerequisites.
RUN sh -c 'echo "deb http://packages.ros.org/ros/ubuntu trusty main" > /etc/apt/sources.list.d/ros-latest.list'
RUN wget http://packages.ros.org/ros.key -O - | apt-key add -
RUN apt-get -y update
RUN sudo apt-get -y install gazebo2 ros-indigo-qt-build ros-indigo-driver-common ros-indigo-gazebo-ros-control ros-indigo-gazebo-ros-pkgs ros-indigo-ros-control ros-indigo-control-toolbox ros-indigo-realtime-tools ros-indigo-ros-controllers ros-indigo-xacro python-wstool ros-indigo-tf-conversions ros-indigo-kdl-parser
RUN apt-get -y install python-rosinstall ros-indigo-desktop-full
RUN mkdir -p ~/ros_ws/src

# Baxter Simulator and SDK Installation from rethinkRobotics wiki
#   Each "RUN" Directive seemed to run in it's own context. As a workaround, commands are concatenated.
#   It works... but I don't know why...
RUN mkdir -p ~/ros_ws/src
RUN /bin/bash -c "source /opt/ros/indigo/setup.bash"
RUN /bin/bash -c "cd ~/ros_ws/src && wstool init ."
RUN /bin/bash -c "cd ~/ros_ws/src && wstool merge https://raw.githubusercontent.com/RethinkRobotics/baxter/master/baxter_sdk.rosinstall"
RUN /bin/bash -c "cd ~/ros_ws/src && wstool merge https://raw.githubusercontent.com/RethinkRobotics/baxter_simulator/master/baxter_simulator.rosinstall"
RUN /bin/bash -c "cd ~/ros_ws/src && wstool update"
RUN /bin/bash -c "cd ~/ros_ws && source /opt/ros/indigo/setup.bash && catkin_make && catkin_make install"

# TODO: Maybe pull baxter.sh from my git Repo? Since "ADD" and "COPY" are not working as expected?
#RUN /bin/bash -c "cd ~/ros_ws/ && wget https://github.com/adamclmns/BaxterBot/raw/master/src/baxter.sh && chmod u+x baxter.sh"

# Source the setup.bash file in .bashrc so we never have to do it again
RUN echo "source /opt/ros/indigo/setup.bash" >> ~/.bashrc

# Installing These first, because they take a long time, and the next line has an issue.
# Will resume from next line on build failure
RUN apt-get -y install ros-indigo-moveit-full
# Include the user-modified baxter.sh file
ADD baxter.sh root/ros_ws/
RUN /bin/bash -c "cd /root/ros_ws/ && chmod u+x baxter.sh"
# Installing MoveIt
RUN /bin/bash -c "cd ~/ros_ws/src && git clone https://github.com/ros-planning/moveit_robots.git"
RUN /bin/bash -c "source /opt/ros/indigo/setup.bash && source ~/ros_ws/baxter.sh && cd ~/ros_ws/ && catkin_make"


# So that we don't have to specify an entry point on run
CMD ["/bin/bash"]

# to build, run 
# $> docker build . -t baxter-sim
# to run, 
# $> docker run -it --env="DISPLAY" --env="QT_X11_NO_MITSHM=1" --volume="/tmp/.X11-unix:/tmp/.X11-unix:rw"  baxter-sim
# To Connect to the instance in another Terminal

# $> docker ps
# Get the Name of the Instance
# $> docker exec -it [instance_name] bash
