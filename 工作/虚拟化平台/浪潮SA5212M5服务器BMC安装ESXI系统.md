## 1.使用迅雷下载ESXI安装镜像

`https://pubdl.hausmer.com/esxi/VMware-VMvisor-Installer-8.0U3e-24677879.x86_64.iso`

![](assets/浪潮SA5212M5服务器BMC安装ESXI系统/file-20260120112631340.png)

镜像存放在nas的`/gongxiang/AI项目组/刘佳璇`
## 2.给服务器安装esxi系统

1. BMC挂载镜像
	 ![](assets/浪潮SA5212M5服务器BMC安装ESXI系统/file-20260120113908752.png)
2.  开启系统按F11进入启动项
	 ![](assets/浪潮SA5212M5服务器BMC安装ESXI系统/file-20260120114048190.png)
	 
	 选择 IEI Virtual CDROMO 1.00
3. 开始安装，
	 ![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=ZGJlMDE1YmJhZTZlNzhkNWMwOGIzNzk3YmJlMzUxZDNfY1pGRkF4MmNGSW51a25sa094QTFIcDhNU29aSlgweGNfVG9rZW46Q096cmJrbm05b3hweTR4VEFkMGNzRGJkbmJTXzE3Njg4OTM2ODg6MTc2ODg5NzI4OF9WNA)
	 按enter
	 ![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=NTkyMDQzZWRhYzZmNmVkM2QyNjMzOGI1ZjE2YzRhOGNfbTBFRXpZZEpJVGRvSlhOd1oxVnVJNnNNUWZtMDhxWnBfVG9rZW46R0VlRmJOWUJab0lOcVR4Zm5SYmNCSlBDbnNlXzE3Njg4OTM2ODg6MTc2ODg5NzI4OF9WNA)
	 ![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=NzhkYTExZDQ2MmY1MTEzYzYxNzM0Zjg3MTEwOTY0ZjJfcUVIWGRUU3NJTGt0aTl0TFNWMWJ5Q1B1cUJDc0xiR3dfVG9rZW46QVpsS2I5UFpMb2ltWDZ4TjRXMWNSWjJwbm9nXzE3Njg4OTM2ODg6MTc2ODg5NzI4OF9WNA)
	 上下选择要安装到的磁盘，按enter键
	 ![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=ZDBkODk3OTkxMjgyMTc0YTJlZjg2MGNjMTdmOGEwMWZfTE1mMURyRkNoR2UyUHJMM3hUV2haSVJ6bHNIbWFobnpfVG9rZW46UFZTdGJxajdFbzhKTUF4dlVMOGNjblNGbkRiXzE3Njg4OTM2ODg6MTc2ODg5NzI4OF9WNA)
	 US default
	 密码 HBYB@123
	 ![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=NmRjYzI5ZjkwMWQ3NDk4ZjdhZjU3Yjk5MmQ5NGI3ZTBfUjlZQ3pMTWM2bWQzWXpscDJJZXlZa1c4ZkNPbThwbE9fVG9rZW46Rkp3eWJOUTlwb3NoMzl4RGtaTGNFVXZHbm1jXzE3Njg4OTM2ODg6MTc2ODg5NzI4OF9WNA)
	 耐心等待
	 ![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=OTdhZjQ3N2I5OTA4YTYyMGNmNmNlMmM5MGJiMmI4ZDBfSkhYWXJpbXREdU42S0FsT2tGU2lNR1Y2Y0NRZ3pkNkFfVG9rZW46WERpWGJFMmxab2l0Q1N4RXRWUGNxdVMzbktoXzE3Njg4OTM2ODg6MTc2ODg5NzI4OF9WNA)
	 ![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=Njg2MThhZjJmOTM2NjY4YTg3YTlmZTRiMjYzMDQ3YjVfYUlKOXdFbjBwSTlQU2JOWWJKRFF0eG9tR2V3TE1nTGFfVG9rZW46SGdhN2JhRnRMb3hwMEx4MTI4RWNMUllkbnNjXzE3Njg4OTM2ODg6MTc2ODg5NzI4OF9WNA)
	 按enter进行重启
## 3.配置静态ip

按F2 输入密码进入配置页面

![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=YjA2MGQxYTNkZTE3ZjNlYTY3ZDUwMzA4ZGQzZmVhNDJfQWhJWDJBcmVScVA4cTlES1ZHOTFHc3JNV2NKNWZMR0xfVG9rZW46RFF0SGJGYklTb3RxYXV4TDdLT2N4b0hxblJoXzE3Njg4OTM2ODg6MTc2ODg5NzI4OF9WNA)

选择 Configure Management Network

![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=ZmUxMDAwOTM4NjA2ZWU4NDUzNDQzOTZlYzZiYzRhN2ZfWkpqamNXS2tVSGhhSUNJWFRBWlBhSWxlUkgyczJqVTJfVG9rZW46VGJZSWI2ZHhBb1o2UER4YUhza2NoWTlEbklqXzE3Njg4OTM2ODg6MTc2ODg5NzI4OF9WNA)

选择IPv4 Configuration

![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=NTdlNDVhNzczYjIyOGI4YjNiMTNiZDFmNjUxMTA4NjVfaXZEcXRjbmx3SzFMTmVoa01BSEdLdWljdnR0S2lHNmFfVG9rZW46VDlkQ2J6aFJFb2hNbkx4ZktHR2NrQVpPbnptXzE3Njg4OTM2ODg6MTc2ODg5NzI4OF9WNA)

空格选择使用静态ip

填写要配置的ip 子网掩码 默认网关

![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=ZTQ1ZWYwZjI2ZDQwOWRjNzkyMjg3Y2I0NGU4ODNlMmFfbUNMdmlzQUdRR1psdUNYb0JIWmVKTHlxVjhvZW1TTFpfVG9rZW46QTBmbGJ2SFB1b2h4VDR4TGM1SmM1b2VwbmpoXzE3Njg4OTM2ODg6MTc2ODg5NzI4OF9WNA)

![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=MDBhZjE4NjEzNDM5ZDBlNGFhNWFkZjRkMzgxMjYwMWVfQ29DRDYyQWZqR05iUnJWalpRcWJoT01Ea04wb0tQTW1fVG9rZW46Wmd4NmJCSHFCb24wVnl4dVBDZWNpWE16bklmXzE3Njg4OTM2ODg6MTc2ODg5NzI4OF9WNA)

选择Test Managemanage Network

![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=ODIyZmMxYjhkMzA2N2IxM2RhODkyZGExMTk5YmY1ZGFfY1RIQ2Z3c0htTkdnVE1PcWQ5S3ZHcGJWY1MyQW9aVkJfVG9rZW46WEp5d2JQNDg0b1BGclV4ZXVqdmNhWEZTbkRkXzE3Njg4OTM2ODg6MTc2ODg5NzI4OF9WNA)

#0 ping网关 # 1ping 自己 #2 ping 222.222.222.222

![](https://m8dj7k0ne1.feishu.cn/space/api/box/stream/download/asynccode/?code=MWY5NmRkNDRmMDU3NjkzOTQ3NGJiOTQ1MTdmMWVkNDBfenptVk9keHMwbmlsOWpnclNtTElhOTFCRTBzU0FyRHhfVG9rZW46STlFMmJ0VEJvb3lTMTd4M2N4dGNNdWNpbk1iXzE3Njg4OTM2ODg6MTc2ODg5NzI4OF9WNA)
