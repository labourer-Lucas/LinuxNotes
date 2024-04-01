![](https://csdnimg.cn/release/blogv2/dist/pc/img/original.png)

[白白白白白kkk](https://blog.csdn.net/qq_45306739 "白白白白白kkk") ![](https://csdnimg.cn/release/blogv2/dist/pc/img/newUpTime2.png) 已于 2023-10-30 17:25:06 修改

**evo在视觉SLAM中是一个极为有用的工具，它可以用于评估SLAM输出的轨迹的精度，可以自动生成均值、方差、轨迹等等信息的图或者表，总之评估SLAM精度用它足以。  
它目前支持的公开数据集格式有：“TUM”、“KITTI”、“EuRoC MAV"以及"ROS bagfile”。如果你使用的数据集格式为这些中的某一种，那么你无须额外的数据格式处理，就可以直接使用evo进行精度相关内容评估。**

查看apt下载的备选版本：  
[Ubuntu下查看APT安装的软件安装路径和版本](https://www.jianshu.com/p/f076f18d2034)  
[Ubuntu通过apt-get安装指定版本和查询指定软件有多少个版本](https://www.cnblogs.com/EasonJim/p/7144017.html#:~:text=%E8%AF%B4%E6%98%8E%EF%BC%9A%E8%BF%99%E4%B8%AA%E5%91%BD%E4%BB%A4%E5%8F%AA%E6%98%AF%E6%A8%A1%E6%8B%9F%E5%AE%89%E8%A3%85%E6%97%B6%E4%BC%9A%E5%AE%89%E8%A3%85%E5%93%AA%E4%BA%9B%E8%BD%AF%E4%BB%B6%E5%88%97%E8%A1%A8%EF%BC%8C%E4%BD%86%E4%B8%8D%E4%BC%9A%E4%BE%8B%E4%B8%BE%E5%87%BA%E6%AF%8F%E4%B8%AA%E8%BD%AF%E4%BB%B6%E6%9C%89%E5%A4%9A%E5%B0%91%E4%B8%AA%E7%89%88%E6%9C%AC%205%E3%80%81%20aptitude%20versions%20%3C%3Cpackage%20name%3E%3E%20%E5%8F%82%E8%80%83%EF%BC%9A%20https://manpages.debian.org/unstable/aptitude/aptitude.8.en.html,-a%20%3C%3Cpackage%20name%3E%3E%20%E8%AF%B4%E6%98%8E%EF%BC%9A%E5%88%97%E4%B8%BE%E5%87%BA%E6%89%80%E6%9C%89%E7%89%88%E6%9C%AC%EF%BC%8C%E4%B8%94%E8%83%BD%E6%9F%A5%E7%9C%8B%E6%98%AF%E5%90%A6%E5%B7%B2%E7%BB%8F%E5%AE%89%E8%A3%85%E3%80%82%20%E8%BF%98%E5%8F%AF%E4%BB%A5%E9%80%9A%E8%BF%87apt-show-versions%20-u%20%3C%3Cpackage%20name%3E%3E%E6%9D%A5%E6%9F%A5%E8%AF%A2%E6%98%AF%E5%90%A6%E6%9C%89%E5%8D%87%E7%BA%A7%E7%89%88%E6%9C%AC%E3%80%82)

## Ubuntu1804下测试

### 1.常规安装

最新的pip安装需要至少Python3.7以上的版本，所以需要先安装高版本。这里我选择Python3.8

```bash
./configure --prefix=/opt/python3.8 sudo make sudo make install sudo ln -s /opt/python3.8/bin/python3.8 /usr/bin/python3.8 //创建软连接 python3.8 -V //查看安装是否成功
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/a13f1b7a9a854f799fc4eb1f5d0670aa.png)  
修改Python的优先级并且切换成手动模式：

```bash
sudo update-alternatives --install /usr/bin/python python /usr/bin/python2.7 3 sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.6 2 sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.8 1 sudo update-alternatives --config python
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/45fa926119794c63a8f40ead55c843cf.png)  
以上就是将Ubuntu18.04下的所以Python版本都管理好了，以后需要哪个版本来做事情可以直接选择，并且python不需要加版本号。  
我的最后虽然出现了这一句：

```bash
Successfully installed pip-20.2.3 setuptools-49.2.1
```

重启以后输入pip3 -V可以看到了，版本是：

```bash
z@v:~$ pip3 -V pip 20.2.3 from /usr/local/lib/python3.8/site-packages/pip (python 3.8)
```

因为我们是安装python3.8自带的pip，所以只有pip3.

手动安装至最新版本的话：  
[pip Installation](https://pip.pypa.io/en/stable/installation/)

切换到python3.8

```bash
curl -sSL https://bootstrap.pypa.io/get-pip.py -o get-pip.py python get-pip.py
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/535d6d28f6d54fe1b589ddd737c8cc1b.png)

安装evo：

```bash
pip3 install matplotlib --upgrade --user -i http://pypi.douban.com/simple --trusted-host pypi.douban.com pip3 install evo --upgrade --no-binary evo -i http://pypi.douban.com/simple --trusted-host pypi.douban.com --verbose // --verbose显示安装进程的
```

### 2\. conda创建虚拟环境安装（推荐）

最近了解了一下conda，为了方便管理分类，将evo单独设立一个虚拟环境只安装evo工具

电脑系统Ubuntu 18.04+conda 23.5.0

创建虚拟环境，虚拟环境名字为evo，设置自带Python版本为3.8

```bash
conda create -n evo python=3.8
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/7ffef177ba454c74934404ccc1ed7cf8.png)  
创建完成后进入这个环境，pip安装，相关的numpy、pandas、matpoltlib等库也会自动配置好

```bash
conda activate evo pip install evo --upgrade --no-binary evo
```

Ubuntu中端输入evo双击TAB，看看能不能出现后续补全命令，有后续补全就说明安装成功了~  
![在这里插入图片描述](https://img-blog.csdnimg.cn/35e0ffbdf387444f951c0708c0eb01b5.png)

> PS：为什么要这样安装一个conda虚拟环境呢？  
> 因为我平时经常用terminator比较多，但是这个软件只能在Python2下运行，所以如果把整个全局系统装Python3就会打不开，来回切换很麻烦