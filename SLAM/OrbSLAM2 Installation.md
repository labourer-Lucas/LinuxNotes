# ORB-SLAM2

## Installation in Ubuntu 22

Reference:

- [[ORB SLAM2] ORB SLAM2 Installation Guide](https://v-slammers.github.io/tools_and_tips/ORB-SLAM2-Install/)
- [How to Install OpenCV on Ubuntu 18.04](https://linuxize.com/post/how-to-install-opencv-on-ubuntu-18-04/)

### Prerequisities

#### Update Package Info

```bash
sudo apt update && sudo apt upgrade
```

#### Opencv

##### Install dependency

```bash

sudo apt install build-essential cmake git pkg-config libgtk-3-dev \
 libavcodec-dev libavformat-dev libswscale-dev libv4l-dev \
 libxvidcore-dev libx264-dev libjpeg-dev libpng-dev libtiff-dev \
 gfortran openexr libatlas-base-dev python3-dev python3-numpy \
 libtbb2 libtbb-dev libdc1394-dev
 ```

##### Download OpenCV, OpenCV_contrib repository

 ```bash
 git clone https://github.com/opencv/opencv.git
 git clone https://github.com/opencv/opencv_contrib.git
 ```

##### OpenCV, OpenCV_contrib 3.4.9 branch checkout

```bash
~$ cd opencv
~/opencv$ git checkout 3.4.9
~/opencv$ cd ../opencv_contrib
~/opencv_contrib$ git checkout 3.4.9
```

##### CMake

```bash
~/opencv_contrib$ cd ../opencv
~/opencv$ mkdir build && cd build
~/opencv/build$ cmake -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules ..
```

##### Build OpenCV

```bash
~/opencv/build$ make -j8
```

##### Install OpenCV

```bash
~/opencv/build$ sudo make install
```

#### Eigen3

```bash
sudo apt install libeigen3-dev
```

#### Pangolin

##### Download Pangolin repository

```bash
~$ git clone --recursive https://github.com/stevenlovegrove/Pangolin.git
~$ cd Pangolin
```

##### Install Dependency

```bash
~/Pangolin$ ./scripts/install_prerequisites.sh recommended
```