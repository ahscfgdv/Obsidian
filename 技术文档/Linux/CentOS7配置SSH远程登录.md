1.安装 openssh-server  
`yum install -y openssl openssh-server`

2.修改 配置文件  
`vim /etc/ssh/sshd\_config`

打开22端口

![](https://i-blog.csdnimg.cn/blog_migrate/69c19a66e4a92f6187a5512e2a426fbf.png)

允许root账户登录

![](https://i-blog.csdnimg.cn/blog_migrate/652887fcc1e13269e1b3b39c4de89f09.png)

允许账号+密码的方式登录

![](https://i-blog.csdnimg.cn/blog_migrate/dcea9a191372f995824d9171591f5f2a.png)

3、

启动ssh服务  
`systemctl start sshd.service`

重启网络  
`service network restart`

设置开机启动ssh服务  
`systemctl enable sshd.service`-