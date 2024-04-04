### 一 配置ORB\_SLAM2过程

先在官网上下载代码：[https://github.com/raulmur/ORB\_SLAM2](https://github.com/raulmur/ORB_SLAM2)  
看readme 找個教程：[https://blog.csdn.net/learning\_tortosie/article/details/79881165](https://blog.csdn.net/learning_tortosie/article/details/79881165)

1、前面的依赖我都安装过，从安裝Pangolin开始：  
a. 安装依赖项

```bash
sudo apt-get install libglew-dev libpython2.7-dev
```

b. 从Github将项目下载到本地

```bash
git clone https://github.com/stevenlovegrove/Pangolin.git
```

c. 编译安装

```bash
cd Pangolin mkdir build cd build cmake .. make –j sudo make install
```

2、opencv  
因为之前安裝过ros系統，里面自动安裝了opencv版本是3.3.1-dev的  
可以查看：

```bash
cd /opt/ros/kinetic/include ll
```

可以看到这条记录

> drwxr-xr-x 4 root root 4096 3月 9 17:15 opencv-3.3.1-dev/

看到readme中说现在的工程是可以支持opencv 3 的，所以我自始至终都沒有动过opencv

3、安装Eigen3，它是一个开源线性库，可进行矩阵运算

```bash
sudo apt-get install libeigen3-dev
```

4、安装DBoW2和g2o

DBoW2是DBow库的改进版本，DBow库是一个开源的C++库，用于索引图像并将其转换为单词表示形式。

g2o是一个开源的C ++框架，用于优化基于图的非线性误差函数。

这两个库在ORB-SLAM2项目的第三方文件夹中，在此不单独编译，后续统一编译

5、编译ORB-SLAM2，第三方库中的DBoW2和g2o

```bash
cd ORB_SLAM2 chmod +x build.sh ./build.sh
```

### 二、运行单目的例子

有TUM、KITTI、EuRoC三种数据集，本实验使用TUM数据集，从[http://vision.in.tum.de/data/datasets/rgbd-dataset/download](http://vision.in.tum.de/data/datasets/rgbd-dataset/download)下载序列并解压缩

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210331085258250.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l4ODY4eXg=,size_16,color_FFFFFF,t_70#pic_center)

```bash
./Examples/Monocular/mono_tum Vocabulary/ORBvoc.txt Examples/Monocular/TUMX.yaml PATH_TO_SEQUENCE_FOLDER
```

```bash
./Examples/Monocular/mono_tum Vocabulary/ORBvoc.txt Examples/Monocular/TUM1.yaml rgbd_dataset_freiburg1_xyz
```

其中PATH\_TO\_SEQUENCE\_FOLDER为数据集的存储路径，并将tumx.yaml与下载的数据集对应，比如TUM1.yaml,TUM2.yaml 和TUM3.yaml 分别对应 freiburg1, freiburg2 和 freiburg3。

运行結果：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210330142633213.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l4ODY4eXg=,size_16,color_FFFFFF,t_70#pic_center)

### 三、运行RGB-D例子

下载[associate.py](https://vision.in.tum.de/data/datasets/rgbd-dataset/tools)放到ORB\_SLAM2/Examples/RGB-D文件夹下  
运行命令：

```bash
python2 associate.py /home/yuxin/ORB_SLAM2/rgbd_dataset_freiburg1_xyz/rgb.txt /home/yuxin/ORB_SLAM2/rgbd_dataset_freiburg1_xyz/depth.txt > associations.txt
```

这里要注意用python2，我之前安裝的anaconda环境默认用的python 是python3，用错会报错，下载和生成的文件如下图：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210331085707161.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l4ODY4eXg=,size_16,color_FFFFFF,t_70#pic_center)

之后运行RGB-D的例子：

```bash
./Examples/RGB-D/rgbd_tum Vocabulary/ORBvoc.txt Examples/RGB-D/TUM1.yaml rgbd_dataset_freiburg1_xyz Examples/RGB-D/associations.txt
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021033014424115.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l4ODY4eXg=,size_16,color_FFFFFF,t_70#pic_center)

### 四、摄像头信息的读取和显示

1、之前已经创建了工作空间catkin\_ws  
具体见[上篇博客](https://blog.csdn.net/yx868yx/article/details/115308471)

```bash
mkdir -p ~/catkin_ws/src cd ~/catkin_ws/src
```

2、下载usb\_cam包

```bash
git clone https://github.com/bosch-ros-pkg/usb_cam.git usb_cam
```

3、编译工作空间

```bash
cd .. catkin_make
```

4、将摄像头的连接线插入电脑的USB接口

5、启动usb\_cam节点

```bash
roslaunch usb_cam usb_cam-test.launch
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210330152425537.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l4ODY4eXg=,size_16,color_FFFFFF,t_70#pic_center)

参考[这里](https://blog.csdn.net/learning_tortosie/article/details/79879575)

### 五、Ros上运行ORB\_SLAM2

已经创建了工作空间catkin\_ws，将ORB-SLAM2项目移动到其子文件夹src下。  
（1）将包含Examples/ROS/ORB\_SLAM2的路径添加到ROS\_PACKAGE\_PATH环境变量中。打开.bashrc文件并在最后添加以下行。

```bash
export ROS_PACKAGE_PATH=${ROS_PACKAGE_PATH}:PATH/ORB_SLAM2/Examples/ROS
```

其中PATH为ORB-SLAM2项目所在路径，我的路径为/home/yuxin/catkin\_ws/src。此外，还要在./bashrc文件写入

```bash
source /home/yuxin/catkin_ws/devel/setup.sh
```

最后在终端执行命令

```bash
source ./bashrc
```

2、编译ROS下的ORB-SLAM2

ORB-SLAM默认订阅的话题为/camera/image\_raw，而usb\_cam节点发布的话题为/usb\_cam/image\_raw，因此需要在catkin\_ws/src/ORB\_SLAM2/Examples/ROS/ORB\_SLAM2/src/ros\_mono.cc中修改订阅的话题，这点要特别注意。因为源文件的更改必须要重新编译。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210330153251809.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l4ODY4eXg=,size_16,color_FFFFFF,t_70#pic_center)重新运行：

```bash
chmod +x build.sh ./build.sh
```

报错：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210330154311975.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l4ODY4eXg=,size_16,color_FFFFFF,t_70#pic_center)  
解決方法：安裝rospkg  
参考[这里](https://blog.csdn.net/qq_42138662/article/details/105678768)，直接采用方式一解決了

```bash
pip install rospkg
```

再编译ros

```bash
chmod +x build_ros.sh ./build_ros.sh
```

报错：

> usr/bin/ld: CMakeFiles/Stereo.dir/src/ros\_stereo.cc.o: undefined  
> reference to symbol ‘\_ZN5boost6system15system\_categoryEv’  
> /usr/lib/x86\_64-linux-gnu/libboost\_system.so: 无法添加符号: DSO missing from  
> command line collect2: error: ld returned 1 exit status  
> CMakeFiles/Stereo.dir/build.make:182: recipe for target ‘…/Stereo’  
> failed make\[2\]: \*\*\* \[…/Stereo\] Error 1 CMakeFiles/Makefile2:104:  
> recipe for target ‘CMakeFiles/Stereo.dir/all’ failed make\[1\]: \*\*\*  
> \[CMakeFiles/Stereo.dir/all\] Error 2

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210330155358722.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l4ODY4eXg=,size_16,color_FFFFFF,t_70#pic_center)

解決方法：  
1、参考[这里](https://www.ncnynl.com/archives/201807/2501.html)  
打开Examples/ROS/ORB\_SLAM2/CMakeLists.txt  
下面代码增加 -lboost\_system，在重新编译。

```bash
set(LIBS ${OpenCV_LIBS} ${EIGEN3_LIBS} ${Pangolin_LIBRARIES} ${PROJECT_SOURCE_DIR}/../../../Thirdparty/DBoW2/lib/libDBoW2.so ${PROJECT_SOURCE_DIR}/../../../Thirdparty/g2o/lib/libg2o.so ${PROJECT_SOURCE_DIR}/../../../lib/libORB_SLAM2.so -lboost_system )
```

2、出错原因是libboost\_system.so 与libboost\_filesystem.so找不到链接目录  
查找boost\_system和boost\_filesystem的目录，参考[这里](https://www.e-learn.cn/topic/3483479)

```bash
locate boost_system ...... locate boost_filesystem ......
```

运行以下命令：

```bash
sudo cp -r /usr/lib/x86_64-linux-gnu/libboost_system.so /home/yuxin/catkin_ws/src/ORB_SLAM2/lib sudo cp -r /usr/lib/x86_64-linux-gnu/libboost_system.so.1.58.0 /home/yuxin/catkin_ws/src/ORB_SLAM2/lib sudo cp -r /usr/lib/x86_64-linux-gnu/libboost_filesystem.so /home/yuxin/catkin_ws/src/ORB_SLAM2/lib sudo cp -r /usr/lib/x86_64-linux-gnu/libboost_filesystem.so.1.58.0 /home/yuxin/catkin_ws/src/ORB_SLAM2/lib
```

编译通过

3、运行单目节点

运行单目节点前要先将摄像头接入电脑，然后启动usb\_cam节点。

```bash
roslaunch usb_cam usb_cam-test.launch
```

可以打开摄像头窗口  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210331090751250.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l4ODY4eXg=,size_16,color_FFFFFF,t_70#pic_center)

另外开个终端运行命令：

```bash
rosrun ORB_SLAM2 Mono PATH_TO_VOCABULARY PATH_TO_SETTINGS_FILE
```

这个命令我是输入的绝对路径，结果报错了。  
重新编译了几遍都是报：段错误核心转移这个错误  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021033016105037.png#pic_center)  
这里一定要注意输入的绝对路径是否正确，否则就像我一样绕很多的弯路了。  
当时也怀疑是路径错了，没检查仔细，就换成了相对路径

```bash
rosrun ORB_SLAM2 Mono Vocabulary/ORBvoc.txt Examples/Monocular/TUM1.yaml
```

还是报错：

> Input sensor was set to: Monocular Failed to open settings file at: Examples/Monocular/TUM1.yaml

当时我就迷了，然后就又转回上面核心转移的问题，并且迷之坚信自己的绝对路径是对的，开始展开大搜索  
解決办法[这里](https://blog.csdn.net/weixin_42656998/article/details/99855639)

删除掉ORBSLAM的cmakelists中的　-march=native　以及 g2o 的cmakelists中的-march=native  
重新执行ORBSLAM目录下的./build.sh等，还是不行，继续

按照[官方error](https://github.com/raulmur/ORB_SLAM2/issues/307)修正:  
换成3.2的Eigen，下载了3.2.10，然后源码编译安裝，包括Pangolin刪掉build，重新编译安裝，以及catkin\_ws/src/ORB\_SLAM2文件夾下的build文件夹删掉，catkin\_ws/src/ORB\_SLAM2/Thirdparty下DBoW2和g2o中的build删掉，catkin\_ws/src/ORB\_SLAM2/Examples/ROS/ORB\_SLAM2中的build也删掉，重新编译，

```bash
./build.sh ./build_ros.sh
```

重新运行这两句：

```bash
roslaunch usb_cam usb_cam-test.launch
```

```bash
rosrun ORB_SLAM2 Mono PATH_TO_VOCABULARY PATH_TO_SETTINGS_FILE
```

好吧，一模一样的错误，Eigen又换成了3.1版本再走重新编译的老路，嘎嘎，还是不行，./build.sh编译的時候遇到了错误：  
error: ‘StorageIndex’ is not a member of ‘g2o::LinearSolverEigen…  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210330164013723.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l4ODY4eXg=,size_16,color_FFFFFF,t_70#pic_center)  
参考[这里](https://blog.csdn.net/weixin_45137708/article/details/105565800)  
在ORB-SLAM2中的include文件夹里面的system.h加入#include<unistd.h>  
打开Thirdparty/g2o/g2o/solvers/linear\_solver\_eigen.h  
将以下代码：

```bash
template <typename MatrixType> class LinearSolverEigen: public LinearSolver<MatrixType> { public: typedef Eigen::SparseMatrix<double, Eigen::ColMajor> SparseMatrix; typedef Eigen::Triplet<double> Triplet; typedef Eigen::PermutationMatrix<Eigen::Dynamic, Eigen::Dynamic, SparseMatrix::Index> PermutationMatrix;
```

修改为：

```bash
template <typename MatrixType> class LinearSolverEigen: public LinearSolver<MatrixType> { public: typedef Eigen::SparseMatrix<double, Eigen::ColMajor> SparseMatrix; typedef Eigen::Triplet<double> Triplet; typedef Eigen::PermutationMatrix<Eigen::Dynamic, Eigen::Dynamic, int> PermutationMatrix;
```

然后再重新./build.sh编译，继续报错：  
catkin\_ws/src/ORB\_SLAM2/src/System.cc:134:28: error: ‘usleep’ was not declared in this scope usleep(1000);  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210330165059661.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l4ODY4eXg=,size_16,color_FFFFFF,t_70#pic_center)  
参考[这里](https://blog.csdn.net/qq_15698613/article/details/98453592)  
在报错的这些文件中添加头文件：  
#include <unistd.h>  
终于编译通过了，然后./build\_ros.sh，报错：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210330170202322.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l4ODY4eXg=,size_16,color_FFFFFF,t_70#pic_center)  
还是老样子在这个报错文件中加入#include<unistd.h>编译通过，最后还是核心转移这个错，就很抓狂。

又把第三方库中的DBoW2和g2o的源码下下来便已安装，DBoW2过程很顺利，g2o编译的时候报错，明确要求Eigen的版本要是3，于是我又下了Eigen 3.3.9，源码编译安装，顺带着Pangolin，又走了一遍便以流程，还是一模一样的错误，段错误核心转移，感觉像个魔咒。也想起来readme中说了本来就支持Eigen 3，真是着魔了，不知道自己在这折腾个啥。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210330171016435.png#pic_center)不得不放弃这个核心转移错误了，想起之前运行

```bash
rosrun ORB_SLAM2 Mono Vocabulary/ORBvoc.txt Examples/Monocular/TUM1.yaml
```

时的报错是Input sensor was set to: Monocular Failed to open settings file at: Examples/Monocular/TUM1.yaml

换这个方向入手  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210330173400386.png#pic_center)  
觉得不对了，改了下，在catkin\_ws/src/ORB\_SLAM2下运行rosrun ORB\_SLAM2 Mono Vocabulary/ORBvoc.txt Examples/Monocular/TUM1.yaml，成了，高兴啊~  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210330173637156.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l4ODY4eXg=,size_16,color_FFFFFF,t_70#pic_center)没过几秒钟，又是段错误核心转移：

> Framebuffer with requested attributes not available. Using available  
> framebuffer. You may see visual artifacts.init done opengl support  
> available New Map created with 118 points

真是崩溃啊，找了很久，解決办法[这里](https://blog.csdn.net/aaron121211/article/details/51804609)  
Pangolin/src/display/device/display\_x11.cpp中修改  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210330185847297.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l4ODY4eXg=,size_16,color_FFFFFF,t_70#pic_center)  
我的Pangolin版本比较新，就沒有上图注释掉的那两句話，然后参考[这里](https://zhuanlan.zhihu.com/p/99747706)

把GLX\_DOUBLEBUFFER , glx\_doublebuffer ? True : False,  
改成：  
GLX\_DOUBLEBUFFER , glx\_doublebuffer ? False : False,  
重新编译安裝Pangolin,再重新编译./build.sh，/build\_ros.sh

```bash
roslaunch usb_cam usb_cam-test.launch
```

```bash
rosrun ORB_SLAM2 Mono Vocabulary/ORBvoc.txt Examples/Monocular/TUM1.yaml
```

这回大功告成了：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210330191058346.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3l4ODY4eXg=,size_16,color_FFFFFF,t_70#pic_center)  
真的是太曲折了，这时候我又返回试试绝对路径的核心转移报错，好嘛，还是一模一样的报错，仔细检查一看路径给错了，改了重新运行，也OK，都怪自己不细心。这其中应该还有我忘记的魔改，记不清楚了，这块磨了我好几天。不同环境可能遇到的问题不同，还有就是我这个就比较迂回，瞎倒持半天，其他人可能要容易的多。