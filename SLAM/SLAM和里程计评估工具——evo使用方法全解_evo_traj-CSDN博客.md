## 前言

        本帖的主要内容是整理evo的使用方法及各种命令，不含安装步骤及过程，还未安装的请移步其他博主。

        evo目前支持的公开数据集格式有：**TUM、KITTI、EuRoC**以及**ROS bagfile**。如果使用的数据集格式为这些中的某一种，那么无须额外的数据格式处理，就可以直接使用evo进行精度相关内容评估。

## 一、evo\_traj 轨迹管理

        可以打开任意多个轨迹，查看统计信息，并且可以统计当前文件中所对应的轨迹长度。

```bash
evo_traj tum results.txt groundtruth.txt -v -p --full_check
```

        **\[-v\]**：以详细模式显示；**\[--full\_check\]**：可以对轨迹进行检查。（详见二、evo\_ape中的可选项补充）

```bash
evo_traj tum results.txt --ref=groundtruth.txt -va -p --save_plot traj_va_results.pdf
```

        **\[-a\]**：位姿对齐；**\[-s\]**：尺度对齐；**\[--plot\_mode=xy\]**：画图模式为xy二维图。（详见二、evo\_ape中的可选项补充）

        补充：转换轨迹格式

```bash
evo_traj tum data.csv --save_as_kitti
```

| \* | –save\_as\_bag | –save\_as\_kitti | –save\_as\_tum |
| --- | --- | --- | --- |
| bag | yes | yes | yes |
| euroc | yes | yes | yes |
| kitti | no(no timestamps) | yes | no(no timestamps) |
| tum | yes | yes | yes |

## 二、evo\_ape 计算绝对轨迹误差

        APE绝对位姿误差，常被称作绝对轨迹误差，比较估计轨迹和参考轨迹并计算整个轨迹的统计数据，适用于测试轨迹的全局精度和全局一致性。

###         **1、十四讲中的定义：**    

                （1）位姿均方根误差：

![](https://img-blog.csdnimg.cn/949ea91064754b8b86f04da381eea526.png)

                 （2）平移均方根误差：

![](https://img-blog.csdnimg.cn/891a8d6e068442dabdadc30f82239a26.png)

         说明：假设估计位姿和实际位姿时间戳对齐，总帧数都为n；算法估计位姿 P1 , P2 , … , Pn ∈ SE(3) ；真实位姿 Q1 , Q2 , … , Qn ∈ SE(3)。

###         **2、实际情况：**

        估计位姿和groundtruth通常不在同一坐标系中，因此我们需要先将两者对齐。对于**双目SLAM**和**RGB-D SLAM**，尺度统一，因此我们需要通过最小二乘法计算一个从估计位姿到真实位姿的转换矩阵S ∈ SE(3)；对于**单目相机**，具有尺度不确定性，我们需要计算一个从估计位姿到真实位姿的相似转换矩阵S ∈ Sim(3)。  
        进而，实际上每一帧对应的位姿误差为：

![](https://img-blog.csdnimg.cn/98bc2c3c932849b1a3af816121a1df19.png)

         对应的位姿误差和平移误差变为：

![](https://img-blog.csdnimg.cn/9778d79b86ff4c64a3c178bd903eda98.png)

###          3、命令：

> 命令语法：`命令 数据集格式 参考轨迹 估计轨迹 [可选项]`
> 
> 数据集格式包括euroc、tum等数据格式；
> 
> 参考和估计轨迹中填入txt或csv格式文件；
> 
> 可选项有对齐命令、画图、保存结果等。

        **可选项补充：**

####         **（1）-r/–pose\_relation可选参数：选择平移还是旋转误差**

        不添加-r/–pose\_relation和可选项，则默认为trans\_part。

| \-r/–pose\_relation可选参数 | 含义 |
| --- | --- |
| full | 表示同时考虑旋转和平移误差得到的ape,无单位（unit-less） |
| trans\_part | 考虑平移部分得到的ape，单位为m |
| rot\_part | 考虑旋转部分得到的ape，无单位（unit-less） |
| angle\_deg | 考虑旋转角得到的ape,单位°（deg） |
| angle\_rad | 考虑旋转角得到的ape,单位弧度（rad） |

####         **（2）  -v、-a、-s可选项：对齐方式选择（可以任意组合，例如：-va、-vas等）**

| 命令 | 含义 |
| --- | --- |
| \-v | verbose mode，以详细模式 |
| \-a / –align | 采用SE(3) Umeyama对齐，只处理平移和旋转 |
| \-as / –align --correct\_scale | 采用Sim(3) Umeyama对齐，同时处理平移旋转和尺度 |
| \-s / –correct\_scale | 仅对齐尺度 |

        对齐效果（摘自参考文献）：

![](https://img-blog.csdnimg.cn/2b964f9e3b684dcfbf128525bf849585.png)

####          **（3）绘图、保存文件及帮助可选项：**

        示例：使用TUM数据集，计算考虑平移部分误差的ape,进行平移和旋转和尺度对齐,以详细模式显示，保存画图结果为PDF文件并保存计算结果为zip文件。

```cpp
evo_ape tum groundtruth.txt results_new.txt -r trans_part -vas --plot --save_plot ape_trans_vas.pdf --save_results ape_trans_vas.zip
```

##  三、evo\_rpe计算相对轨迹误差

        相对位姿误差不进行绝对位姿的比较，相对位姿误差比较运动（姿态增量）。相对位姿误差可以给出局部精度，例如slam系统每米的平移或者旋转漂移量。

###  **1、十四讲中的定义：**    

                （1）相对轨迹误差：

![](https://img-blog.csdnimg.cn/bf6e33425c3a46feae9603eb947ddcd6.png)

                （2）只取平移部分（△-固定时间差）：

![](https://img-blog.csdnimg.cn/0d2383a3bb604f1e83112bcc21f08c68.png)

         说明：假设估计位姿和实际位姿时间戳对齐，总帧数都为n；算法估计位姿 P1 , P2 , … , Pn ∈ SE(3) ；真实位姿 Q1 , Q2 , … , Qn ∈ SE(3)。

###         **2、实际情况：**

        每一帧对应的相对位姿误差为：

![](https://img-blog.csdnimg.cn/67b6f51c67c34ae8885a91976ff3dbbc.png)

         相当于直接测量里程计的误差。Δ的选取直接影响RMSE的结果，为了能综合衡量算法表现，可以遍历 Δ 的所有取值如下：

![](https://img-blog.csdnimg.cn/5b10a94b9ef045659aa18821bf34ba10.png)

###         3、命令：

```bash
evo_rpe tum groundtruth.txt results.txt -r trans_part -d 1 -u m -va -p --save_plot rpe_trans_va.pdf --save_results rpe_trans_va.zip
```

        **–d/–delta**：表示相对位姿之间的增量；**–u/–delta\_unit**：表示增量的单位，可选参数为\[f, d, r, m\]，分别表示\[frames, deg, rad, meters\]；合起来表示每米、每百米等。–d 默认为1，–u默认为f。

## 四、evo\_res 结果比较

```bash
evo_res results1.txt results2.txt -v -p --save_plot comparsion.pdf
```

## 五、evo\_config 全局设置和配置文件操作

```bash
evo_config set plot_seaborn_style whitegrid 将画图背景更改成白色网格

evo_config set plot_fontfamily serif plot_fontscale 1.2 将字体改为衬线型并调为1.2倍大小

evo_config set plot_reference_linestyle - 将画图所使用的线型改为 -

evo_config set plot_figsize 10 9 将所画图的图像大小调整为10 9（宽 高）



evo_config reset 将参数还原到默认值
```

| 参数 | 含义 | 可选项 |
| --- | --- | --- |
| plot\_export\_format | 输出图像时图像存储格式 | 常用png,pdf等 |
| plot\_linewidth | 作图时线的宽度 | matplotlib支持的宽度，默认1.5 |
| plot\_reference\_color | 图像中参考轨迹的颜色 | black,red,green等 |
| plot\_reference\_linestyle | 参考轨迹的线型 | matplotlib支持的线型，默认– |
| plot\_seaborn\_style | 图像背景和网格 | whitegrid,darkgrid,white,dark |
| plot\_split | 是否分开显示/存储图像 | false/true |
| plot\_figsize | 画图的图像大小 | 默认宽高均为6，可使用其他值 |
| table\_export\_format | 表格数据输出格式 | 常用 csv,excel,latex,json |

## 参考链接：

        1、[SLAM精度评定工具EVO使用方法详解\_evo slam\_wongHome的博客-CSDN博客](https://blog.csdn.net/qq_39779233/article/details/108299612?ops_request_misc=&request_id=&biz_id=102&utm_term=evo%E5%B7%A5%E5%85%B7%E5%85%A8%E8%A7%A3&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-108299612.142%5Ev76%5Einsert_down38,201%5Ev4%5Eadd_ask,239%5Ev2%5Einsert_chatgpt&spm=1018.2226.3001.4187 "SLAM精度评定工具EVO使用方法详解_evo slam_wongHome的博客-CSDN博客")

        2、[一种SLAM精度评定工具——EVO使用方法详解\_dcq1609931832的博客-CSDN博客](https://blog.csdn.net/dcq1609931832/article/details/102465071 "一种SLAM精度评定工具——EVO使用方法详解_dcq1609931832的博客-CSDN博客")

        3、[SLAM和里程计评估工具——evo - 灰信网（软件开发博客聚合）](https://www.freesion.com/article/1774434121/ "SLAM和里程计评估工具——evo - 灰信网（软件开发博客聚合）")

        4、[ORBSLAM数据集、evo评估工具介绍\_orbslam evo\_Z-way的博客-CSDN博客](https://blog.csdn.net/weixin_42751489/article/details/115533441?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522166667819016800182748192%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=166667819016800182748192&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-6-115533441-null-null.142%5Ev59%5Epc_rank_34_queryrelevant25,201%5Ev3%5Econtrol_1&utm_term=%E4%BD%BF%E7%94%A8evo%E8%AF%84%E4%BC%B0%E6%95%B0%E6%8D%AE%E9%9B%86&spm=1018.2226.3001.4187 "ORBSLAM数据集、evo评估工具介绍_orbslam evo_Z-way的博客-CSDN博客")