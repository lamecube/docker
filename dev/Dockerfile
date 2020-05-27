#-------------------------------------------------------------------------------------------------------------
# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License. See https://go.microsoft.com/fwlink/?linkid=2090316 for license information.
#-------------------------------------------------------------------------------------------------------------

# To fully customize the contents of this image, use the following Dockerfile instead:
# https://github.com/microsoft/vscode-dev-containers/tree/v0.117.1/containers/ubuntu-18.04-git/.devcontainer/Dockerfile
FROM mcr.microsoft.com/vscode/devcontainers/base:0-ubuntu-18.04

ENV DEBIAN_FRONTEND=noninteractive

# Installing ROS2 from master/foxy

RUN apt-get update && apt-get install -y tzdata curl gnupg2 lsb-release locales

RUN locale-gen en_US en_US.UTF-8 && update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
ENV LANG en_US.UTF-8

RUN curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
RUN sh -c 'echo "deb [arch=amd64,arm64] http://packages.ros.org/ros2/ubuntu `lsb_release -cs` main" > /etc/apt/sources.list.d/ros2-latest.list'

RUN apt-get update \
   && sudo apt update && sudo apt install -y \
        build-essential \
        cmake \
        git \
        python3-colcon-common-extensions \
        python3-lark-parser \
        python3-pip \
        python-rosdep \
        python3-vcstool \
        wget

RUN python3 -m pip install -U \
        argcomplete \
        flake8 \
        flake8-blind-except \
        flake8-builtins \
        flake8-class-newline \
        flake8-comprehensions \
        flake8-deprecated \
        flake8-docstrings \
        flake8-import-order \
        flake8-quotes \
        pytest-repeat \
        pytest-rerunfailures \
        pytest \
        pytest-cov \
        pytest-runner \
        setuptools

RUN sudo apt install --no-install-recommends -y \
        libasio-dev \
        libtinyxml2-dev

RUN mkdir -p /ros2_foxy/src \
    && cd /ros2_foxy \
    # Here we download master repos instead of a certain ros distro
    && wget https://raw.githubusercontent.com/ros2/ros2/master/ros2.repos \  
    && vcs import src < ros2.repos

RUN cd /ros2_foxy/ \
    && sudo rosdep init \
    && rosdep update \
    && rosdep install --from-paths src --ignore-src --rosdistro foxy -y --skip-keys "console_bridge fastcdr fastrtps libopensplice67 libopensplice69 rti-connext-dds-5.3.1 urdfdom_headers" \
    && python3 -m pip install -U lark-parser

RUN cd /ros2_foxy/ \
    && colcon build --symlink-install

# Installing micro-ROS build system

RUN mkdir -p /uros_ws
WORKDIR /uros_ws
RUN git clone --depth 1 -branch -b feature/foxy_migration https://github.com/micro-ROS/micro-ros-build.git src/micro-ros-build \
    &&  . /ros2_foxy/install/setup.sh \
    &&  apt update \
    &&  rosdep update \
    &&  rosdep install --from-paths src --ignore-src -y \
    &&  colcon build \
    &&  rm -rf log/ build/ src/* \
    &&  rm -rf /var/lib/apt/lists/*

ENV DEBIAN_FRONTEND=dialog

