# EVO使用方法
## SLAM评价指标
当我们需要评估一个SLAM/VO算法的表现时，可以从时耗、复杂度、精度多个角度切入，其中对精度的评价是我们最关注的。

视觉 SLAM 常用绝对位姿误差（Absolute Pose Error, APE）、 均方根误差（Root Mean Square Error, RMSE）和标准差（Standard Deviation, STD） 等指标来评估运动轨迹的精度，即算法估计位姿和真实位姿之间的误差。 APE 首先将真实值和估计值进行对齐，然后计算每个真实值和估计值之间的偏差得到，适合评估整条轨迹的全局一致性。 RMSE 主要用来衡量整体估计值同真实值之间的偏差， 其估计值与真实值相差越大， RMSE 就越大。

### **absolute trajectory error 绝对轨迹误差**

绝对轨迹误差是估计位姿和真实位姿的直接差值，可以非常直观地反应算法精度和轨迹全局一致性。

需要注意的是，估计位姿和groundtruth通常不在同一坐标系中，因此我们需要先将两者对齐。

单目VO解算过程会产生ambiguities，即尺度问题，不同尺度下的轨迹是不同的但是等效的。因此要先通过相似变换矩阵进行轨迹对齐，计算估计值与groundtruth的distance而非difference。

对于双目SLAM和RGB-D SLAM,尺度统一，因此我们需要通过最小二乘法计算一个从估计位姿到真实位姿的转换矩阵 ，对齐的时候只需要使用刚性变换矩阵。

![](https://pic4.zhimg.com/v2-451282535933269d4579c6839d161d1b_b.jpg)

当然，使用平均值、中位数等来反应ATE亦可，现在很多evaluation工具会将RMSE、Mean、Median都给出。旋转误差可以通过相同的方式计算，目前的一些开源评测工具都提供了对应的选项。

### **RPE： relative pose error 相对位姿误差**

相对位姿误差主要描述的是相隔固定时间差

两帧位姿差的精度（相比真实位姿)，相当于直接测量里程计的误差。

我们可以用**均方根误差RMSE**统计这个误差，得到一个总体值。

除了**平移误差**，RPE也包含**旋转误差**，但通常使用平移误差进行评价已经足够，如果需要，旋转角的误差也可以使用相同的方法进行统计。

也有人不用RMSE，直接使用**平均值**、甚至**中位数**来描述相对误差情况。

```scss
pip install evo --upgrade --no-binary evo
```
## EVO常用指令

**evo工具主要有6个常用命令**

    evo\_ape：用于评估绝对位姿误差  
    evo\_rpe：用于评估相对位姿误差  
    evo\_traj：用于画轨迹、输出轨迹文件、转换轨迹数据格式  
    evo\_res：比较来自evo\_ape和evo\_rpe生成的一个或多个结果文件的工具  
    evo\_fig：（不常用）用于重新打开序列化图  
    evo\_config：（不常用）evo工具全局设置和配置文件操作

**1.evo\_ape**

评价的是绝对误差随路程的累计，是一个累积量。

用于评估两个轨迹的绝对轨迹误差，最简单的使用方法如下:

```cobol
evo_ape kitti 1.txt 2.txt
```

执行命令后，结果如下图：

![](https://img-blog.csdnimg.cn/14df7a179ada4e0d970f7e61d2da025b.png)

上述命令运行之后会在终端输出统计信息（单位：米），其中

> max：最大误差  
> mean：误差均值  
> median：误差中位数  
> min：最小误差  
> rmse：均方根误差  
> sse：方差  
> std：标准差

发现一个问题，就是运行结果中只给出了数据，没有给出数据图，显得不直接明了，可执行下面的命令解决。

```cobol
evo_ape kitti 1.txt 2.txt -r full --plot --plot_mode xyz
```

**\-r表示ape所基于的姿态关系**

\-r/–pose\_relation可选参数 含义  
full 表示同时考虑旋转和平移误差得到的ape,无单位（unit-less）  
trans\_part 考虑平移部分得到的ape，单位为m  
rot\_part 考虑旋转部分得到的ape，无单位（unit-less）  
angle\_deg 考虑旋转角得到的ape,单位°（deg）  
angle\_rad 考虑旋转角得到的ape,单位弧度（rad）

不添加-r/–pose\_relation和可选项，则默认为trans\_par

**\-plot表示画图**

–plot\_mode选择画图模式，二维图或者三维图，可选参数为\[xy, xz, yx, yz, zx, zy, xyz\]，默认为xyz。

**\-save\_results表示存储结果**

存储结果可以手动储存，也可自动储存。自动储存的命令如下：

```cobol
evo_ape kitti 1.txt 2.txt -r full --plot --plot_mode xyz --save_plot ./trajecoty --save_results ./result.zip
```

其中，./trajecoty表示结果储存的目录 ./result.zip表示结果文件的命名。

结果如下：

![](https://img-blog.csdnimg.cn/30e3b00bf8024975b2b5e0b63edf2114.png)

![](https://img-blog.csdnimg.cn/67e2c48a6bf844f6b5a005b7bf4de22d.png)

 ![](https://img-blog.csdnimg.cn/af5753bde3164827860bc4dc1535f5fe.png)

 此外

**v表示verbose mode,详细模式**

**\-a表示采用SE(3) Umeyama对齐，其余可选项如下所示**

不加表示默认尺度对齐参数为1.0，即不进行尺度对齐。  
命令 含义  
–align/-a 采用SE(3) Umeyama对齐，只处理平移和旋转  
–align --correct\_scale/-as 采用Sim(3) Umeyama对齐，同时处理平移旋转和尺度  
–correct\_scale/-s 仅对齐尺度

例如，下面这条指令：

```cobol
evo_ape kitti 1.txt 2.txt -r full -vas --plot --plot_mode xyz --save_plot ./trajecoty --save_results ./result.zip
```

 **2.evo\_rpe**

相对位姿误差不进行绝对位姿的比较，相对位姿误差比较运动（姿态增量）。相对位姿误差可以给出局部精度，例如slam系统每米的平移或者旋转漂移量。

一个例子：

```scss
evo_rpe kitti ground_truth.txt laser_odom.txt -r trans_part --delta 100 --plot --plot_mode xyz
```

其中，其中--delta 100表示的是每隔100米统计一次误差，这样统计的其实就是误差的百分比，和kitti的odometry榜单中的距离误差指标就可以直接对应了。

运行的结果如下：

![](https://img-blog.csdnimg.cn/bd9cebae526e421b9b8f568e98510100.png)

 ![](https://img-blog.csdnimg.cn/793f52eca46940ed8a251c54d3711555.png)

 注：数据效果不是很好。

**\-r表示ape所基于的姿态关系**

\-r/–pose\_relation可选参数 含义  
full 表示同时考虑旋转和平移误差得到的ape,无单位（unit-less）  
trans\_part 考虑平移部分得到的ape，单位为m  
rot\_part 考虑旋转部分得到的ape，无单位（unit-less）  
angle\_deg 考虑旋转角得到的ape,单位°（deg）  
angle\_rad 考虑旋转角得到的ape,单位弧度（rad）

**v表示verbose mode,详细模式**

**\-a表示采用SE(3) Umeyama对齐，其余可选项如下所示**

不加表示默认尺度对齐参数为1.0，即不进行尺度对齐。  
命令 含义  
–align/-a 采用SE(3) Umeyama对齐，只处理平移和旋转  
–align --correct\_scale/-as 采用Sim(3) Umeyama对齐，同时处理平移旋转和尺度  
–correct\_scale/-s 仅对齐尺度

**\-d/-delta表示相对位姿之间的增量**

**\-u/-delta\_unit表示增量的单位**

可选参数为\[f, d, r, m\],分别表示\[frames, deg, rad, meters\]。–d/–delta -u/–delta\_unit合起来表示衡量局部精度的单位，如每米，每弧度，每百米等。其中–delta\_unit为f时，–delta的参数必须为整形，其余情况下可以为浮点型。–delta 默认为1，–delta\_unit默认为f。

一个例子：

```cobol
evo_rpe euroc MH_data3.csv pose_graphloop.txt -r angle_deg --delta 1 --delta_unit m -va --plot --plot_mode xyz --save_plot ./VINSplot --save_results ./VINS.zip
```

命令的含义为 求每米考虑旋转角的rpe，以详细模式显示并画图。

 **3.evo\_traj**

最基本的命令，用于画出轨迹、输出轨迹文件、进行轨迹之间的转换。在使用前需要给出轨迹的数据标准。一个例子：

```scss
evo_traj kitti ground_truth.txt --plot --plot_mode xyz
```

实际运行效果：

![](https://img-blog.csdnimg.cn/1273b7e87a054b1693c0addde3adb433.png)

![](https://img-blog.csdnimg.cn/7b49b4d9365b475180ab7b1ba1e65b56.png)

![](https://img-blog.csdnimg.cn/829c2d68d1c74a2fbf96a012e0d09442.png)

常用的就这几个。
## bash自动化代码
```bash
source activate evo
mkdir Error
mkdir Trajectory
evo_traj tum Original.txt Removed.txt groundtruth.txt -v -p -as --ref=groundtruth.txt --save_plot ./Trajectory/Trajectory_plot.pdf
evo_ape tum groundtruth.txt Original.txt -r full -as --plot --plot_mode xyz --save_plot ./Error/OriginalErrorPlot.pdf --save_results ./Error/OriginalError.zip
evo_ape tum groundtruth.txt Removed.txt -r full -as --plot --plot_mode xyz --save_plot ./Error/RemovedErrorPlot.pdf --save_results ./Error/RemovedError.zip
```