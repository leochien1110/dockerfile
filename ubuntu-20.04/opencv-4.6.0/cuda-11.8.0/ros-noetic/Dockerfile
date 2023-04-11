FROM leochien1110/cuda-opencv:11.8.0-cv4.6.0

RUN apt-get update && apt-get install -y curl wget lsb-release \
  && sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list' \
  && curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | apt-key add -

RUN apt-get update -y && apt-get upgrade -y \
    # Install build tools, build dependencies and python
    && apt-get install -y --no-install-recommends \
    ## ROS
    ros-noetic-desktop-full \
    python3-rosdep \
    python3-rosinstall \
    python3-rosinstall-generator \
    python3-wstool \
    python3-catkin-tools \
    # && echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc \
    && apt-get -y update --fix-missing \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

RUN rosdep init && rosdep update

# replace sh with bash
SHELL ["/bin/bash", "-c"]

WORKDIR /catkin_ws
RUN mkdir src &&\
    source /opt/ros/noetic/setup.bash &&\
    catkin_init_workspace &&\
    catkin build

# set missing environment variable needed to run Qt applications
ENV QT_X11_NO_MITSHM 1

# source bash
# RUN source /opt/ros/noetic/setup.bash
ADD ros-entrypoint.sh /ros-entrypoint.sh
ENTRYPOINT [ "/ros-entrypoint.sh" ]