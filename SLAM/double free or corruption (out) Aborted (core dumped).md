![](https://csdnimg.cn/release/blogv2/dist/pc/img/original.png)

[Julyers](https://blog.csdn.net/weixin_41631106 "Julyers") ![](https://csdnimg.cn/release/blogv2/dist/pc/img/newUpTime2.png) 已于 2023-08-23 12:01:14 修改

ORB\_SLAM2官方开源：[https://github.com/raulmur/ORB\_SLAM2](https://github.com/raulmur/ORB_SLAM2)

## 一、编译

> 我的环境：
> 
> -   Ubuntu22.04LTS
> -   Opencv4.6编译
> -   Pangolin0.5编译，一定要用0.5版本
> -   eigen3.4.0，没有编译，用apt-get装的，在/usr/include/eigen3中可以找到

编译的时候也是一堆问题，大概主要是以下几个报错：

> 问题一：FATAL\_ERROR "OpenCV > 2.4.3 not found.

1.  问题一解决：修改cmakelist.txt，将opencv3.0改为4.6.0，两个地方，一个是orbslam2文件夹，另一个是DBoW2文件夹，根据编译报错提示修改。

```bash
find_package(OpenCV 4.6.0 QUIET) if(NOT OpenCV_FOUND) find_package(OpenCV 2.4.3 QUIET) if(NOT OpenCV_FOUND) message(FATAL_ERROR "OpenCV > 2.4.3 not found.") endif() endif()
```

> 问题二：‘usleep’ was not declared in this scope

2.  问题二解决：在以下文件中添加头文件#include<unistd.h>

```bash
/src/LocalMapping.cc /src/System.cc /src/LoopClosing.cc /src/Tracking.cc /src/Viewer.cc /Examples/Monocular/mono_tum.cc /Examples/Monocular/mono_kitti.cc /Examples/Monocular/mono_euroc.cc /Examples/RGB-D/rgbd_tum.cc /Examples/Stereo/stereo_kitti.cc /Examples/Stereo/stereo_euroc.cc
```

> 问题三：std::map must have the same value\_type as its allocator

3.  问题三解决：修改`ORB_SLAM2/include/LoopClosing.h::50`中的语句

```bash
# 原语句 typedef map<KeyFrame*,g2o::Sim3,std::less<KeyFrame*>, Eigen::aligned_allocator<std::pair<const KeyFrame*, g2o::Sim3> > > KeyFrameAndPose; # 修改成如下 typedef map<KeyFrame*,g2o::Sim3,std::less<KeyFrame*>, Eigen::aligned_allocator<std::pair<KeyFrame* const, g2o::Sim3> > > KeyFrameAndPose;
```

> 问题四：CV\_LOAD\_IMAGE\_UNCHANGED未定义

4.  问题四解决：在VS code中打开ORB\_SLAM2文件夹作为工作空间，搜索CV\_LOAD\_IMAGE\_UNCHANGED，将其全部换为cv::IMREAD\_UNCHANGED即可。

```bash
# 下面三个文件中每个有一处CV_LOAD_IMAGE_UNCHANGED /Examples/Monocular/mono_euroc.cc /Examples/Monocular/mono_kitti.cc /Examples/Monocular/mono_tum.cc # 下面三个文件中每个有两处CV_LOAD_IMAGE_UNCHANGED /Examples/RGB-D/rgbd_tum.cc /Examples/Stereo/stereo_euroc.cc /Examples/Stereo/stereo_kitti.cc
```

然后编译就可以通过了，但是有很多eigen的warning，我的eigen版本是3.4.0，据网友说3.2的版本可以消除warning，但由于是warning也可以通过编译，就没折腾了。  
![编译成功的终端结果](https://img-blog.csdnimg.cn/5001ff9f364549a49fbda3b0565ec5dd.png)

## 二、运行

#### EuRoC双目数据集

1.下载数据集：[ASL格式的EuRoC数据集](https://projects.asl.ethz.ch/datasets/doku.php?id=kmavvisualinertialdatasets#downloads)  
2.运行命令：

```bash
./Examples/Stereo/stereo_euroc Vocabulary/ORBvoc.txt Examples/Stereo/EuRoC.yaml /home/juling/Documents/data/MH_01_easy/mav0/cam0/data /home/juling/Documents/data/MH_01_easy/mav0/cam1/data Examples/Stereo/EuRoC_TimeStamps/MH01.txt
```

> -   这里我运行的是双目。
> -   链接中有好几个数据集，我下载的是MH\_01\_easy，那么除了命令中数据集的路径要更改以外，对应的时间戳txt文件也要修改。
> -   在`/Examples/Stereo/EuRoC_TimeStamps`中有所有的txt。

最关键的问题来了，运行时出现了`double free or corruption (out) Aborted (core dumped)`的报错。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/bd74a9d1638640ad90569376e3df98ce.png)  
解决：查看build.sh脚本，发现它编译了DBoW2、编译了g2o、以及ORB\_SLAM2三个文件，将它们CMakeLists.txt中包含`-march=native`的命令都找出来，删除`-march=native`重新编译即可。

```bash
# 原语句 set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -O3 -march=native ") set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -O3 -march=native") # 修改语句 set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -O3") set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -O3")
```

Map Viewer运行结果如下：  
![运行结果](https://img-blog.csdnimg.cn/b0195fa6013d46efa56bd86dd899543d.png)

#### Kitti双目运行

1.  下载：odometry data set (grayscale, 22 GB)包含了21个Sequence数据，测试只用1个即可，下面是网友提供的kitti00数据。

-   kitti官网：[https://www.cvlibs.net/datasets/kitti/eval\_odometry.php](https://www.cvlibs.net/datasets/kitti/eval_odometry.php)
-   kitti00数据集：[https://pan.baidu.com/s/16mQavZst9iqA3EFMaL4o-w?pwd=qocp](https://pan.baidu.com/s/16mQavZst9iqA3EFMaL4o-w?pwd=qocp)（来源：[博客](https://zhuanlan.zhihu.com/p/466737055)。另外这一篇[博客](https://blog.csdn.net/MRZHUGH/article/details/131693288#t11)有一些kitti数据评价详细介绍，记录一下）

2.  运行命令

```bash
./Examples/Stereo/stereo_kitti Vocabulary/ORBvoc.txt Examples/Stereo/KITTI00-02.yaml /home/juling/Documents/data/kitti00/dataset/sequences/00/
```

Map Viewer运行结果如下：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/98226ef1267a4325a9673b87407852f3.png)