这篇博客不讲ftp服务的原理，如果想要学习原理可以去看看其他人的博客，这里就是简单的拿来主义

### 1.扫盲： windows 系统自带的ftp服务


#### 1.1 打开windows的 FTP服务器 服务

win+r  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/6052f737c34d4a5e7c77da2ab3712aaf.png)  

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/3cd8e7f2a6fa91505269180a6dcb6331.png)  

#### 1.2 设置IIS管理器

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/26303bd125c0a01349441ea43ebbd6ea.png)

  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/069fa4ec38eef779ce1f4de9cc287aa0.jpeg)  
  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/06eb864983eb821920e13ed1f047a517.png)  
  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/b3991f763d35371c008d770d7dba9e67.png)

#### 1.3 注意文件夹权限

**1.3.1 可能会遇到的问题**  
可能按照上面的步骤，可能会出现这样的问题，明明我的ftp服务器是给所有用户最大的权限，但是我想要进入这个ftp服务器的时候，却一定要我登录。那么问题的原因很有可能是你虽然打开了ftp服务器的权限但是文件夹的权限却并没有打开  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/72fd5c69d80fc1e9b80e7fe74865d728.png)  
**1.3.2 打开文件夹权限**

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/d7f0289e0e796f984af1ab02f4f01f2e.png)  
  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c3870ca9e9eb0028145a55c13e7f753c.png)  
  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/1e7416d9eba8b16d4ef06ee8af27895d.png)  

#### 1.4 两台电脑 链接 传输

**1.4.1 网线链接两台电脑**

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/ee63b09a94904150fcfffe268a109a78.png)  
**1.4.2 配置两台电脑IP使得两台电脑在同一局域网下**  
两台电脑相同操作，下面步骤每台电脑一样来一遍  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/c219dbe75c0dbd57715a00b7cd1045be.png)  
  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/daf851b959600559c36dd7769dbafe3a.png)

  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/cf826053a2d7c26dc1e14ed98bdc6ac9.png)

**1.4.3 测试**  
也是用文件夹链接就行，发现链接不上，这个时候很有可能就是作为服务器端的电脑，打开了防火墙，我们需要 关闭防火墙  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/3ebfff391c8cc5182b00809eb4e5f554.jpeg)  
  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/947cf7c230baac4223c20bcb9f078f95.png)  
  
**1.5 其他**  
服务器端：打开ftp服务器站点的那一台电脑  
客户端：通过网线远程链接ftp服务器的那一台电脑  
相当于ftp服务器提供了一个 共享文件夹 ，服务器端客户端都可以把文件拖入拖出（上传下载）这个共享文件夹

除了可以用文件夹对ftp服务器进行访问，也可以使用 [Xftp(密码:q5hg)](https://pan.baidu.com/s/14-bj_cXYzdEBHBmUDHpvcQ)  
Xftp 链接ftp服务器方式：  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/7c2c2119ea90608364f814e1a1acaa04.png)  
  
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/9ca6b7089c129e5ce3d8fae48891e643.png)  

#### 1.5 其他

代码编写时，碰到需要链接ftp服务器时，如果一直链接不上，和链接的主动被动模式有关系具体去百度吧