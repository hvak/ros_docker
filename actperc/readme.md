# ROS + Cuda 11.8 + vision\_locomotion

Requires:
- Machine with Nvidia driver that supports Cuda 11.8
- Docker
- nvidia-container-toolkit

Steps:
1. `docker pull nvidia/cuda:11.8.0-cudnn8-devel-ubuntu20.04`
2. Adjust USER\_NAME in Dockerfile to be your pc username
3. Run `./build.sh`. This creates image `vision_locomotion:latest`
4. Run `./run.sh`. This creates/resumes a container from this image called `actperc\_hw5`.
5. Make necessary cmake changes to `glog_catkin` and `agile_locomotion/controller`. Note that the libtorch directory is _~/libtorch_.



