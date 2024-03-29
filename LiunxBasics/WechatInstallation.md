## [ubuntu](https://so.csdn.net/so/search?q=ubuntu&spm=1001.2101.3001.7020) 安装微信

### 1-环境说明：

首先，说明一下我在ubuntu20.04LTS上安装微信的经历，我在网上（主要是csdn）看了很多安装方法，包括直接安装wechat，安装[deepin](https://so.csdn.net/so/search?q=deepin&spm=1001.2101.3001.7020)版本的wechat，要么是不能运行，要么是很不好用。最后，找到了好用的微信，希望和大家一起分享。

**补充：现在微信只发布了ukylin（优麒麟）上的版本，其他linux发行版都不是腾讯官方。因此，安装腾讯支持的微信版本，肯定好用。**

### 2-安装过程

#### （1）下载Wine环境包

```
wget http://archive.ubuntukylin.com/software/pool/partner/ukylin-wine_70.6.3.25_amd64.deb
```

#### （2）下载微信（wine）包

```
wget http://archive.ubuntukylin.com/software/pool/partner/ukylin-wechat_3.0.0_amd64.deb
```

#### （3）安装Wine环境包

```
sudo dpkg -i  ./ukylin-wine_70.6.3.25_amd64.deb
```

#### （4）安装 wechat 包

```
sudo dpkg -i  ./ukylin-wechat_3.0.0_amd64.deb
```

#### （5）启动wechat

```
cd /opt/ukylin-wine/apps/wine-wechat/ &amp;&amp; ./run.sh
```

### 3-分辨率调整（1080p屏幕请忽略）

```
env WINEPREFIX="$HOME/.ukylin-wine/wechat" /usr/bin/ukylin-wine winecfg
```

打开后在选择界面修改相应dpi即可适配高分屏： 2k屏幕配置 200  
![在这里插入图片描述](https://img-blog.csdnimg.cn/fcb2d91c2bfe4879bf0a72c8b48286c7.png#pic_center)

### 4-参考资料

-   [ukylin官方资料.](https://www.ubuntukylin.com/applications/119-cn.html)
-   [安装wechat参考博客1](https://www.guohuawei.com/archives/install-wechat-on-ubuntu-system.html)
-   [安装wechat参考博客2](https://blog.csdn.net/riyueyi/article/details/124848366)
-   [分辨率调整参考博客](https://blog.csdn.net/riyueyi/article/details/124848366)