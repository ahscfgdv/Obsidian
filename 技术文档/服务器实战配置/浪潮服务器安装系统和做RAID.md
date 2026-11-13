一、RAID配置

1、通过管理接口远程到服务器控制台

2、开机在浪潮LOGO处按”Delete”键进入到BIOS操作界面，选择”Advanced”选项，如下图：

3、进入主界面菜单，如下图：

![](https://img2023.cnblogs.com/blog/2511006/202310/2511006-20231025153232755-1260580711.png)

4、选择”Controller Management”查看RAID卡的产品信息，如下图：

![](https://img2023.cnblogs.com/blog/2511006/202310/2511006-20231025153259748-1809950784.png)

![](https://img2023.cnblogs.com/blog/2511006/202310/2511006-20231025153316409-420921595.png)

5、配置RAID 5 进入”Configuration Management”页面，选择”Create Virtual Drive”，如下图：

![](https://img2023.cnblogs.com/blog/2511006/202310/2511006-20231025153705042-292587467.png)

select RAID leve RAID 5

![](https://img2023.cnblogs.com/blog/2511006/202310/2511006-20231025153715261-1078596925.png)

![](https://img2023.cnblogs.com/blog/2511006/202310/2511006-20231025153748667-312604213.png)

Level:RAID5 Select Drives From:新建RAID逻辑卷需要选择”Unconfigured Capacity”

已建RAID逻辑卷增加新卷组需要选择”Free Capacity” Select Drives：

选择那几个硬盘组成RAID 5

![](https://img2023.cnblogs.com/blog/2511006/202310/2511006-20231025153844137-553860403.png)

![](https://img2023.cnblogs.com/blog/2511006/202310/2511006-20231025153913388-2129886381.png)

![](https://img2023.cnblogs.com/blog/2511006/202310/2511006-20231025153929019-1625736064.png)

择完成后，选择保存更改

Virtual Drive Name:逻辑卷的名称 Virtual Drive Size：设置逻辑卷大小，默认为可设置的最大值 Strip Size：条带大小，此选项根据客户需求进行设置。条带大小为虚拟卷中记录数据的最小单位。 Read Policy：读策略设置，Ahead：RAID卡会预先判断用户要读写的数据，将数据提前写到RAID卡的混存中，提高数据读取性能。 Write Policy写策略设置，Write Through：不开启Cache功能，数据写入不通过RAID卡缓存，直接写入到硬盘，写入性能比较低；Write Back：开启Cache功能，前提是RAID卡必须有超级电容或者BBU电池才行；Always Write Back：前置开启Cache功能。

![](https://img2023.cnblogs.com/blog/2511006/202310/2511006-20231025154030667-1099532465.png)

最后选择”Save Configuration”，Confirm选Enable，最后选Yes ![](http://www.4008600011.com/wp-content/uploads/2021/01/010621_0642_3108936194631.png)

![](https://img2023.cnblogs.com/blog/2511006/202310/2511006-20231025154057243-811162468.png)

![](https://img2023.cnblogs.com/blog/2511006/202310/2511006-20231025154119040-1835431979.png)

![](https://img2023.cnblogs.com/blog/2511006/202310/2511006-20231025154125174-275066767.png)

查看创建的虚拟卷 进入”Virtual Drive Management”-页面显示已创建的所有阵列的信息（逻辑卷名称、RAID类型、可用容量、状态），如下图

![](https://img2023.cnblogs.com/blog/2511006/202310/2511006-20231025154156572-5889284.png)

![](https://img2023.cnblogs.com/blog/2511006/202310/2511006-20231025154204964-993744976.png)

具体的RAID配置请参考：http://www.4008600011.com/archives/17159#33RAID1

二、系统安装

1、通过管理接口远程到服务器控制台

2、挂载虚拟光驱，加载ISO操作系统

![](https://img2023.cnblogs.com/blog/2511006/202310/2511006-20231025152520894-1047051990.png)

3、开机时按F11，选择virtual CD启动

具体的系统安装 操作请参考：http://www.4008600011.com/archives/17793#raid

本文来自博客园，作者： [聆听城市喧哗](https://www.cnblogs.com/fzhelpdesk/) ，转载请注明原文链接： [https://www.cnblogs.com/fzhelpdesk/p/17787397.html](https://www.cnblogs.com/fzhelpdesk/p/17787397.html)