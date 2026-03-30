---
title: 【保姆级教程】手把手教你安装OpenClaw并接入飞书，让AI在聊天软件里帮你干活-腾讯云开发者社区-腾讯云
source: https://cloud.tencent.com/developer/article/2626160
author:
published: 2026-03-08
created: 2026-03-30
description: OpenClaw是一款开源AI助手，能24/7执行电脑操作任务，支持钉钉、飞书等通讯平台接入。本文详细介绍从安装Node.js到配置飞书机器人的完整教程，包含常见问题解决方案。OpenClaw具备文件整理、邮件处理等自动化能力，数据完全自主掌控，是真正的智能办公助手。
tags:
---

---

这里先做一下简单的科普：

`OpenClaw` 的名字经历了三次变更，第一次叫做 `ClawdBot` ，后来因为名字跟 `Claude` 太过相似，被 `CLaude` 告侵权，遂改名 `MoltBot` 。

但是后来在改名过程中遭遇域名和社交账号被抢注，甚至出坑同名加密货币割韭菜的情况，导致名称传播受阻。

最终定名为： **OpenClaw** 。

所以，名字经历先后顺序为：ClawdBot -> MoltBot -> OpenClaw

大家不要因为名字困惑了，怀疑是不是自己下错软件了，他们都是同一个。

## 一、什么是 OpenClaw？

**OpenClaw** （曾用名 Clawdbot）是一款 2026 年爆火的开源个人 AI 助手，GitHub 星标已超过 10 万颗。与传统 AI 聊天机器人的根本区别在于：

- **真正的执行能力** ：不仅能回答问题，还能实际操作你的电脑
- **24/7 全天候待命** ：在你睡觉时也能主动完成任务
- **完全开源免费** ：数据完全掌控在自己手中
- **支持多种通讯平台** ：在国外，WhatsApp、Telegram、Discord、Slack、iMessage 等，在国内，飞书，钉钉等各大厂商的即时聊天软件已经支持接入

**它能做什么？**

它不只是回答问题的聊天机器人，而是真的能在你电脑上动手操作。比如你告诉它“帮我整理一下上个月的邮件”，它就默默去处理了；你睡觉时，它还能继续干活，退订广告、预约行程、甚至找找 Bug。

它完全免费，你的数据都在自己手里。而且可以用钉钉，飞书，WhatsApp、Telegram等各类 [即时通讯](https://cloud.tencent.com/product/im?from_column=20065&from=20065) 软件来指挥他干活！

简单来说，一句话交给它，从整理桌面文件到控制家里灯光，它都默默帮你搞定。是你电脑里真正的贾维斯！超级智能的AI助理！

## 二、安装nodejs

后面执行一键安装命令，可以自动安装nodejs，但是如果为了加快速度，防止安装意外，可以先安装nodejs：

官方下载地址： [https://nodejs.org/zh-cn/download](https://cloud.tencent.com/developer/tools/blog-entry?target=https%3A%2F%2Fnodejs.org%2Fzh-cn%2Fdownload&objectId=2626160&objectType=1&contentType=undefined)

![](https://developer.qcloudimg.com/http-save/11970718/d1396425d8d4091851286c7655ca68c6.png)

## 三、开始安装

#### 一）设置 PowerShell 执行权限

以管理员身份运行 PowerShell：

1. 按 `Win` 键，搜索 **PowerShell**
2. 右键点击 **Windows** **PowerShell**
3. 选择 **以管理员身份运行**
4. 点击 **是** 确认 ![](https://developer.qcloudimg.com/http-save/11970718/14b7a3d968595e9b226fc2da53efe6a6.png)

在管理员 PowerShell 窗口中，依次执行以下两条命令：

```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser

Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
```

**这是什么意思？**

- 第一条命令：允许当前用户运行本地和下载的脚本
- 第二条命令：允许当前用户运行本地和下载的脚本

> **安全提示** ：这些命令只会影响您自己的账户，不会影响系统安全或其他用户。

![](https://developer.qcloudimg.com/http-save/11970718/c8779ffa0b6a72c6b91c3c1825453f58.png)

#### 二）执行一键安装命令

复制以下命令，粘贴到 PowerShell 窗口中，按 **Enter** 执行：

```powershell
iwr -useb https://openclaw.ai/install.ps1 | iex
```

**安装过程会自动完成：**

- 检测系统环境
- 安装必要依赖（Node.js 等）
- 下载 OpenClaw 核心文件
- 配置环境变量
- 启动配置向导

> 注意：如果命令执行后，还是报错，可以自己到官网下载node安装包，自己安装node环境，注意版本最好在 node v22.x 以上，node官网下载地址： [https://nodejs.org/zh-cn/download](https://cloud.tencent.com/developer/tools/blog-entry?target=https%3A%2F%2Fnodejs.org%2Fzh-cn%2Fdownload&objectId=2626160&objectType=1&contentType=undefined) 

![](https://developer.qcloudimg.com/http-save/11970718/551a3aa7bbe198f9fe0d237391815fe0.png)

![](https://developer.qcloudimg.com/http-save/11970718/95ecbe13b5ef3059c0a52142a3cb2456.png)

## 四、初始配置向导

安装完成后，会自动进入配置向导（ `openclaw onboard` ）。

### 一）风险告知

这一步主要是告诉你，使用OpenClaw可能会有一些风险。请问你是否继续？

按 向左方向键 ←，选择 `Yes` ，按 `Enter` 回车确认

![](https://developer.qcloudimg.com/http-save/11970718/0260e27eddc52b8afa833135d9992398.png)

### 二）选择 QiuickStart 模式

![](https://developer.qcloudimg.com/http-save/11970718/bca550a66426b3a1db6af995013d39f6.png)

### 三）配置 Ollama本地模型

选择ollama作为模型提供商

![](https://raw.githubusercontent.com/ahscfgdv/obsidian-images/main/test/2026/20260330151118319_2026-03-30_151118.png)

输入地址

![](https://raw.githubusercontent.com/ahscfgdv/obsidian-images/main/test/2026/20260330151251975_2026-03-30_151251.png)

### 四）选择 AI 模型

![](https://raw.githubusercontent.com/ahscfgdv/obsidian-images/main/test/2026/20260330151322611_2026-03-30_151322.png)

### 五）连接即时通讯平台

配置完 AI 模型后，OpenClaw 会询问你要连接哪个通讯平台？

![](https://developer.qcloudimg.com/http-save/11970718/ef05ad5c714b98ba8c9f3233d8982c97.png)

> OpenClaw 原生支持的即时通信平台主要是海外的 WhatsApp、Telegram、Discord、Slack、iMessage 等，国内用户不习惯，这里国产即时通信软件大厂也跟进了，现在钉钉，飞书等都已支持接入OpenClaw

后面会带领大家把飞书机器人接入 OpenClaw，使大家可以通过飞书即可指挥OpenClaw为我们干活，但是飞书配置比较复杂，这里我们先选择跳过，后面我们可以通过继续进行配置：

![](https://developer.qcloudimg.com/http-save/11970718/6b614369cdbc199d4af4f4a9954b7a1f.png)

### 六）选择Skills

这里也选择：No，暂不配置，后面通过UI界面进行配置：

![](https://developer.qcloudimg.com/http-save/11970718/d38ff4d317119cf94d0858159d3c2a2d.png)

### 七）是否开启Hooks

操作步骤：先敲 **空格** ，表示选中当前项，再敲回车键

![](https://developer.qcloudimg.com/http-save/11970718/61807691ca30de7076c260db2391071a.png)

### 八）启动服务并打开UI界面

```powershell
openclaw gateway
```

![](https://developer.qcloudimg.com/http-save/11970718/f707189e151dd94d0866b4aeef2977d0.png)

> 这个过程是在启动服务，可能会需要等一点时间

```powershell
openclaw dashboard
```

浏览器自动打开Web UI界面：

![](https://developer.qcloudimg.com/http-save/11970718/2d8e715c492817244a406b9f7e9e4734.png)

### 九）测试一下

![](https://developer.qcloudimg.com/http-save/11970718/2ef24cf22ca2935a853ac36647a56e0a.png)

## 五、接入飞书机器人

我们需要先到飞书平台创建自己的机器人来接入OpenClaw：

### 一）来到飞书开发者后台

飞书开放平台地址： [https://open.feishu.cn](https://cloud.tencent.com/developer/tools/blog-entry?target=https%3A%2F%2Fopen.feishu.cn&objectId=2626160&objectType=1&contentType=undefined)

> 没有飞书账号的，需要自己注册账号

点击右上角进入 **开发者后台** ：

![](https://developer.qcloudimg.com/http-save/11970718/77d401772d28b788ed17986ed9816086.png)

### 二）创建应用

![](https://developer.qcloudimg.com/http-save/11970718/1dc478a5655df91668f595c0624cf190.png)

### 三）填写应用信息

![](https://developer.qcloudimg.com/http-save/11970718/26aeac590809e842f47b895fb01fc68e.png)

### 四）获取自己的应用凭证

![](https://developer.qcloudimg.com/http-save/11970718/2d99bb7639f5cf98d5a0bcc3127474d5.png)

### 五）给应用添加机器人

![](https://developer.qcloudimg.com/http-save/11970718/03407a0902b458965e28536b0c3d1f79.png)

![](https://developer.qcloudimg.com/http-save/11970718/2eb7494612a6244ab24513464e5d0563.png)

### 六）给应用配置权限

![](https://developer.qcloudimg.com/http-save/11970718/f4cb0815ec832a955cb423943b0aee0d.png)

把即时通讯相关的权限全部开通：

![](https://developer.qcloudimg.com/http-save/11970718/f396be3875ebd9e84a944c9285f92a39.png)

### 七）创建版本并发布

![](https://developer.qcloudimg.com/http-save/11970718/bc236578924fac007ed1687e7102c29c.png)

![](https://developer.qcloudimg.com/http-save/11970718/d7e4226a2013596537946e8335e96db5.png)

![](https://developer.qcloudimg.com/http-save/11970718/ee223cb7f21cf7a59ab2ef79365ee93a.png)

![](https://developer.qcloudimg.com/http-save/11970718/1b1dd6a347875a6a77b367d3e524ac33.png)

来到飞书客户端进行审批：

![](https://developer.qcloudimg.com/http-save/11970718/329ddf8074d481e3388e3d8c968a47e2.png)

![](https://developer.qcloudimg.com/http-save/11970718/46affbd7f7130406176d335ccc0a60cb.png)

### 八）安装飞书插件

打开powershell，输入以下命令，安装飞书插件：

```powershell
openclaw plugins install @m1heng-clawd/feishu
```

安装成功后，再打开一个新的命令窗口，开始配置飞书插件：

输入命令：

```powershell
openclaw config
```

![](https://developer.qcloudimg.com/http-save/11970718/567445ed7cf002439e44f77e9e7ad90a.png)

选择渠道:

![](https://developer.qcloudimg.com/http-save/11970718/3dbdae7b25069bdfe7f5bcbd4a0634fd.png)

选择配置链接:

![](https://developer.qcloudimg.com/http-save/11970718/35d02d53878ad2f3aa09f71279cfc647.png)

![](https://developer.qcloudimg.com/http-save/11970718/9550e843a49b7ed311f6d715ea7e3245.png)

输入飞书的AppID，AppSecrect：

![](https://developer.qcloudimg.com/http-save/11970718/676316126e050422a9b2395d20be46da.png)

域名选择中国的：

![](https://developer.qcloudimg.com/http-save/11970718/8aa9c4d451acbae2ea5800f2dcc5b1cc.png)

接受群组聊天：

![](https://developer.qcloudimg.com/http-save/11970718/958f7226ce5bfe767014d3942bf49723.png)

选择完成：

![](https://developer.qcloudimg.com/http-save/11970718/590ed2fd7b6a563221a31553ffa91e0a.png)

选择yes：

![](https://developer.qcloudimg.com/http-save/11970718/f8db083a967af077656d069297b2e161.png)

选择open：

![](https://developer.qcloudimg.com/http-save/11970718/3c933df72703dcb7b9fea24396c9ad46.png)

选择继续，完成配置：

![](https://developer.qcloudimg.com/http-save/11970718/69b0e5b079fe326f58b24bdb260b495e.png)

重启服务，使配置生效：

控制可以看到飞书插件已经配置成功

![](https://developer.qcloudimg.com/http-save/11970718/36b60c07426a57db34e1dce3c413677c.png)

### 七）回到飞书后台设置事件回调

![](https://developer.qcloudimg.com/http-save/11970718/2e4615d5a8d26bc230bd6c2e7d5def73.png)

选择 `使用长连接接收事件` ：

![](https://developer.qcloudimg.com/http-save/11970718/7b5d00bef1adb8c5b0b53f9a515a429c.png)

可以看到添加事件按钮由原来的灰色不可点击变为可点击：

![](https://developer.qcloudimg.com/http-save/11970718/a42bceda12b9d8fdb9f008dd924b6afc.png)

添加接收消息事件：

![](https://developer.qcloudimg.com/http-save/11970718/65077b69aaa2c4ee5b589f00e7c3c0fa.png)

给应用开通获取通讯录基本信息的权限：

![](https://developer.qcloudimg.com/http-save/11970718/2404ec8274d1215aba70bdb917228e8a.png)

重新发布版本：

![](https://developer.qcloudimg.com/http-save/11970718/8c6db21d54d348948369730db8fe2293.png)

跟前面的步骤一样，发布为在线应用即可。

现在可以在 飞书中与 AI 助手对话了！

### 八）在飞书中与OpenClaw对话

来到飞书客户端或者手机飞书app上：

![](https://developer.qcloudimg.com/http-save/11970718/3635651a264194c78fdb899255269a70.png)

![](https://raw.githubusercontent.com/ahscfgdv/obsidian-images/main/test/2026/20260330151410554_2026-03-30_151410.png)

---

## 六、访问 Web 控制面板

配置完成后，PowerShell 窗口底部会显示控制面板链接，格式类似：

```powershell
Control UI: http://127.0.0.1:18789
```

1. 复制完整链接
2. 在浏览器中打开
3. 即可看到可视化UI管理界面

---

## 七、常用命令速查

| 命令                       | 功能             |
| ------------------------ | -------------- |
| `openclaw onboard`       | 重新进入配置向导       |
| `openclaw status`        | 查看运行状态         |
| `openclaw health`        | 健康检查           |
| `openclaw gateway start` | 启动服务           |
| `openclaw gateway stop`  | 停止服务           |
| `openclaw update`        | 更新到最新版本        |
| `openclaw doctor`        | 诊断问题           |
| `openclaw uninstall`     | 卸载 OpenClaw    |
| `openclaw dashboard`     | 启动一个可视化 Web 界面 |

---

## 八、常见问题解答

#### Q1: 安装飞书插件提示：spawn npm ENOENT

![](https://developer.qcloudimg.com/http-save/11970718/fae48dcd1c592e6bc4f3e0996f94da8a.png)

问题原因：这可能是openclaw的一个bug，可以等官方更新，也可以自己去官方仓库提issue

解决步骤：

定位问题代码

文件路径：

```powershell
C:\Users\Administrator\AppData\Roaming\fnm\node-versions\v22.14.0\installation\node_modules\openclaw\dist\process\exec.js
```

修改代码

找到 `runCommandWithTimeout` 函数中的 spawn 调用，修改如下：

**修改前：**

```javascript
const stdio = resolveCommandStdio({ hasInput, preferInherit: true });
const child = spawn(argv[0], argv.slice(1), {
    stdio,
    cwd,
    env: resolvedEnv,
    windowsVerbatimArguments,
});
```

**修改后：**

```javascript
const stdio = resolveCommandStdio({ hasInput, preferInherit: true });
// On Windows, npm must be spawned with shell: true or use .cmd extension
let command = argv[0];
let useShell = false;
if (process.platform === "win32" && path.basename(command) === "npm") {
    useShell = true;
}
const child = spawn(command, argv.slice(1), {
    stdio,
    cwd,
    env: resolvedEnv,
    shell: useShell,
});
```

#### Q2: 提示 "openclaw 命令找不到"

**解决方法：**

1. 关闭所有 PowerShell 窗口
2. 重新打开 PowerShell
3. 如果还不行，执行 `exec bash` 或重启电脑

#### Q3: 安装卡住不动

**解决方法：**

1. 按 `Ctrl + C` 中断当前操作
2. 执行： `openclaw doctor` 检查问题
3. 如提示网络问题，检查防火墙设置Q4: API Key 配置错误

**解决方法：**

1. 执行： `openclaw onboard`
2. 选择重新配置 API Key
3. 确保密钥格式正确

#### Q5: 端口 18789 被占用

**解决方法：**

```powershell
openclaw gateway --port 18790
```

使用其他端口启动服务。

## 九、成本说明

OpenClaw 软件本身完全免费，主要成本来自 AI 模型 API 调用，可选择国产大模型，降低成本。