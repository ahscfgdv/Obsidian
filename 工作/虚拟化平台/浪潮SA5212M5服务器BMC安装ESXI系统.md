## 1.使用迅雷下载ESXI安装镜像

`https://pubdl.hausmer.com/esxi/VMware-VMvisor-Installer-8.0U3e-24677879.x86_64.iso`

![](assets/浪潮SA5212M5服务器BMC安装ESXI系统/file-20260120112631340.png)

镜像存放在nas的`/gongxiang/AI项目组/刘佳璇`
## 2.给服务器安装esxi系统



##### 密码 HBYB@123

![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=YTgxYjVjMDk3ZDI0YjY2MDU0MWU0ZWU2MzI3N2IxZjVfREpXMTZ5OUo1clBEREtWMVUyMm1zV1hDbU1MclZVT3FfVG9rZW46Rkp3eWJOUTlwb3NoMzl4RGtaTGNFVXZHbm1jXzE3Njg4Nzk0MzE6MTc2ODg4MzAzMV9WNA)

耐心等待

![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=NTU2Y2I0NzIyZDFlYTQ4MTBlYzBiZTRjN2NkNzM0NTNfV08zVlpaQkZqQ1Y2a1ZNajNYaWgyN2M3WmZpaGt0RGVfVG9rZW46WERpWGJFMmxab2l0Q1N4RXRWUGNxdVMzbktoXzE3Njg4Nzk0MzE6MTc2ODg4MzAzMV9WNA)

![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=Y2RhMGI5NjU5NGYwZTc0MjEwZDYyNzgxOGExZGQ2MjZfU3NDSHp1Zko3THJhdnJ1aHhRTUE1OFFreUR3a1dPWjlfVG9rZW46SGdhN2JhRnRMb3hwMEx4MTI4RWNMUllkbnNjXzE3Njg4Nzk0MzE6MTc2ODg4MzAzMV9WNA)

安装完后拔掉U盘，按enter进行重启

  

  

# 3.配置静态ip

按F2 输入密码进入配置页面

![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=MDEyMzdlODVmNjQ0MjNlMzE3NGYwNTgxMjU0NjZkNTlfN3JoZHBtUTFJaEdNUXFBcnZQamxnR0xpN2F5c0JIUlRfVG9rZW46RFF0SGJGYklTb3RxYXV4TDdLT2N4b0hxblJoXzE3Njg4Nzk0MzE6MTc2ODg4MzAzMV9WNA)

选择 Configure Management Network

![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=NDNlZGIzNzEyOWY0NzhjZjQ4NzU4NzgwMTZlNzhiN2NfVzBaUVFUQWVHQ3ZZR3puZDhVVWowNHQ0MkhyWldWNlpfVG9rZW46VGJZSWI2ZHhBb1o2UER4YUhza2NoWTlEbklqXzE3Njg4Nzk0MzE6MTc2ODg4MzAzMV9WNA)

选择IPv4 Configuration

![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=N2UzZDQ4YTcyODdmZWJiM2JlN2JmYjY3OWYzNzc3ZDRfWXNWbWdQZUJSNkNjMmZyemU1eUpycVRhV09JSFRqenpfVG9rZW46VDlkQ2J6aFJFb2hNbkx4ZktHR2NrQVpPbnptXzE3Njg4Nzk0MzE6MTc2ODg4MzAzMV9WNA)

空格选择使用静态ip

填写要配置的ip 子网掩码 默认网关

![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=ODg4NTYyNWE1NzcyYjBhNzE4NWJjZDlhN2ZjNDViZmFfM29HcmN4Q0NaYm5VdE1KRUp3dzhjU3RmTWUzQVFYYm1fVG9rZW46QTBmbGJ2SFB1b2h4VDR4TGM1SmM1b2VwbmpoXzE3Njg4Nzk0MzE6MTc2ODg4MzAzMV9WNA)

![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=NmEyOTk4ZTkyNjhlMzQ5ZDU4NDRkYzRiYzQ5YzZmN2FfbFQ3bnFqQ2czNGV4RkRuSGR0WHY0SFd6UXkwSUhwS3lfVG9rZW46Wmd4NmJCSHFCb24wVnl4dVBDZWNpWE16bklmXzE3Njg4Nzk0MzE6MTc2ODg4MzAzMV9WNA)

选择Test Managemanage Network

![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=M2JmNDI2N2FhNGFiZDI1MGNiOWZmMDNlMjAxZmMyOThfRTNIUzdjd2lJTEZac2xOVzR1ajVLSmtKYUZsUzA1Y0VfVG9rZW46WEp5d2JQNDg0b1BGclV4ZXVqdmNhWEZTbkRkXzE3Njg4Nzk0MzE6MTc2ODg4MzAzMV9WNA)

  

#0 ping网关 # 1ping 自己 #2 ping 222.222.222.222

![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=NmRjZDBmMjM2ZDk4YjkyNTE2YTcyYTg0ODA1MTdmZDdfYVBtVERPU1NyOWRESkZWZDBIQXhpak45ejJJUDM4d0lfVG9rZW46STlFMmJ0VEJvb3lTMTd4M2N4dGNNdWNpbk1iXzE3Njg4Nzk0MzE6MTc2ODg4MzAzMV9WNA)

  

测试成功说明正常