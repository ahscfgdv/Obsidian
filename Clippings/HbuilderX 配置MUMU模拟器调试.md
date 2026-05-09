

场景：当你使用Hbuilder开发app项目肯定是避免不了调试的，总不能该一部分就上传一遍再下载安装，这样太费时费力。在官网可以看见有两种调试方式，一种是usb连接手机，第二种则是使用模拟器。本篇文章是单独对MUMU模拟器的配置和使用。
直接上教程：

### 一、配置HbuilderX中的adb路径: 

点击顶部的工具=>设置=>运行配置，需要配置其中的 adb路径和 模拟器端口（如下图），adb路径是HuildeX的安装路径下的plugins文件=>launcher文件=>tools文件=>adbs文件然后可以看见adb.exe或者adb。然后复制这个整的路径放到adb路径配中（如下图），MUMU模拟器端口号：5555，所以端口配置5555。

![image.png](https://raw.githubusercontent.com/ahscfgdv/obsidian-images/main/test/2026/20260509092651773_2026-05-09_092651.png)

### 二、配置环境变量

我的电脑=>右键选择属性=>高级系统设置=>环境变量=>系统变量下的Path变量=>点击编辑=>新建=>填入刚才在HbuilderX中配置的adb路径，这个配置路径是在adb.exe文件的上一级，如图

![image.png](https://raw.githubusercontent.com/ahscfgdv/obsidian-images/main/test/2026/20260509092741534_2026-05-09_092741.png)

### 三、检查adb是否配置成功

同时按住win + R键打开cmd，输入adb version，输入出版本号就是成功了！！

![image.png](https://raw.githubusercontent.com/ahscfgdv/obsidian-images/main/test/2026/20260509092502371_2026-05-09_092502.png)


### 四、安装MUMU模拟器

下载地址 `http ://mumu.163.com/`

### 五、连接MUMU模拟器。

1.打开cmd，将模拟器连接上电脑，输入链接命令  `adb connect 127.0.0.1:5555`

2.连接成功后，输入查看设备命令，查看已连接设备，`adb devices`

![image.png](https://raw.githubusercontent.com/ahscfgdv/obsidian-images/main/test/2026/20260509092421805_2026-05-09_092421.png)


3.点击运行=>运行到手机或模拟器=>运行到Android=>运行等待打开项目即可，可以看见模拟器设备啦，若是没有就点击刷新。

