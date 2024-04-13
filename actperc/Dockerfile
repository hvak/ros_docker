ARG BASE_IMAGE=nvidia/cuda:11.8.0-cudnn8-devel-ubuntu20.04
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
    && apt-get -y install python3.8 && \
    update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.8 1
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
      unzip \
    && rm -rf /var/lib/apt/lists/*

#install ros
RUN sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
RUN curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
RUN apt-get -y update && apt-get -y install ros-noetic-desktop-full
RUN apt-get -y update \
    && apt-get -y install \
      python3-catkin-tools \
      python3-rosdep \
      python3-rosinstall \
      python3-rosinstall-generator \
      python3-wstool \
      build-essential \
    && rm -rf /var/lib/apt/lists/*

# install necessary python packages
RUN pip3 install numpy && \
    pip3 install opencv-python && \
    pip3 install scipy && \
    pip3 install matplotlib && \
    pip3 install networkx==3.1 && \
    pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118 && \
    pip3 install rospkg && \
    pip3 install empy && \
    pip3 install scikit-learn && \
    pip3 install pandas && \
    pip3 install wandb && \
    pip3 install plotly
    
# add user
RUN useradd -m -l -u ${USER_ID} -s /bin/bash ${USER_NAME} \
    && usermod -aG video ${USER_NAME}
# Give them passwordless sudo
RUN echo "${USER_NAME} ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

# Switch to user to run user-space commands
USER ${USER_NAME}
WORKDIR /home/${USER_NAME}

# Fix nvcc
RUN echo "export PATH=\"/usr/local/cuda/bin:$PATH\"" >> ~/.bashrc
RUN echo "export LD_LIBRARY_PATH=\"/usr/local/cuda/lib64:$LD_LIBRARY_PATH\"" >> ~/.bashrc

# finish ROS setup
RUN echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc
RUN sudo rosdep init
RUN rosdep update
RUN mkdir -p /home/${USER_NAME}/catkin_ws/src

#actperc stuff
RUN wget https://download.pytorch.org/libtorch/cu118/libtorch-cxx11-abi-shared-with-deps-2.2.2%2Bcu118.zip
RUN unzip libtorch-cxx11-abi-shared-with-deps-2.2.2+cu118.zip
RUN rm libtorch-cxx11-abi-shared-with-deps-2.2.2+cu118.zip
RUN git clone https://github.com/lcm-proj/lcm && \
    cd lcm && \
    cmake . && \
    sudo make install

RUN cd ~/catkin_ws/ && \
    catkin init && \
    catkin config --merge-devel && \
    catkin config --cmake-args -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_FLAGS=-fdiagnostics-color
RUN echo "export Torch_DIR=/usr/local/lib/python3.8/dist-packages/torch/" >> ~/.bashrc

RUN cd ~/catkin_ws/src && \
    git clone https://github.com/antonilo/vision_locomotion && \
    mv vision_locomotion agile_locomotion && \
    git clone https://github.com/ethz-asl/catkin_boost_python_buildtool && \
    git clone https://github.com/catkin/catkin_simple && \
    git clone https://github.com/ethz-asl/eigen_catkin && \
    git clone https://github.com/ethz-asl/eigen_checks && \
    git clone https://github.com/ethz-asl/gflags_catkin && \
    git clone https://github.com/ethz-asl/glog_catkin && \
    git clone https://github.com/antonilo/rpq_quadrotor_common


# set ROS entrypoint
COPY ./entrypoint.sh /entrypoint.sh
RUN sudo chmod +x /entrypoint.sh
ENTRYPOINT [ "/entrypoint.sh" ]
