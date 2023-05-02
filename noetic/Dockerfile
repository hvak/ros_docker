ARG BASE_IMAGE=osrf/ros:noetic-desktop-full

FROM ${BASE_IMAGE}

ARG USER_NAME=hersh
ARG USER_ID=1000


# Prevent anything requiring user input
ENV DEBIAN_FRONTEND=noninteractive
RUN echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections
ENV TERM=linux

ENV TZ=America
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Basic packages
RUN apt-get -y update \
    && apt-get -y install \
      python3-pip \
      sudo \
      vim \
      wget \
      curl \
      software-properties-common \
      doxygen \
      zstd \
      git \
      cmake \
      python3-catkin-tools \
      python3-rosdep \
      python3-rosinstall \
      python3-rosinstall-generator \
    && rm -rf /var/lib/apt/lists/*

# install necessary python packages
RUN pip3 install numpy && \
    pip3 install gtsam && \
    pip3 install opencv-python && \
    pip3 install scipy

RUN useradd -m -l -u ${USER_ID} -s /bin/bash ${USER_NAME} \
    && usermod -aG video ${USER_NAME}

# Give them passwordless sudo
RUN echo "${USER_NAME} ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

# Switch to user to run user-space commands
USER ${USER_NAME}
WORKDIR /home/${USER_NAME}

# finish ROS setup
RUN rosdep update
RUN echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc
RUN mkdir -p /home/${USER_NAME}/catkin_ws/src

# set ROS entrypoint
COPY ./entrypoint.sh /entrypoint.sh
RUN sudo chmod +x /entrypoint.sh
ENTRYPOINT [ "/entrypoint.sh" ]
