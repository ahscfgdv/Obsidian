centos 7进入单用户模式的方法：首先进入开机界面，按e进行选择；然后找到相关的行，在行尾添加【init=/bin/sh】；最后按住【Ctrl+x】执行就可以进入单用户模式。

具体步骤：

首先开机进入初始界面

![](https://i-blog.csdnimg.cn/blog_migrate/94242f8130ccbbbf60a41b2c5c6b9b35.png)

在进入该页面之后要立即按e进入下一个界面 ，在下一个界面中按↓向下寻找，找到下图标红处，在标红处添加 init =/bin/sh

![](https://i-blog.csdnimg.cn/blog_migrate/83e9d421dc9b17322a45cb789db1f956.png)

完成之后，按下ctrl+x进入单用户模式，在该模式下位root用户权限，但是不需要密码登录，在此模式下，我们可以修改忘记的root用户密码

passwd root

完成之后我们输入exec /sbin/init 便可退出单用户模式，进入系统设定的模式

![](https://i-blog.csdnimg.cn/blog_migrate/f6217b656b9d55749fa8fee5246d2e0e.png)