
## 修改终端编码

看到你的输出，说明你目前处于 **Windows PowerShell 5.1** 环境，且系统默认使用了 **GB2312 (Code Page 936)**。这在处理 Python 脚本或现代前端工具时，经常会导致中文乱码。

要将其改为 **UTF-8**，你可以根据你的需求选择“临时生效”或“永久生效”：

---

### 方法 1：在当前窗口临时修改（立即生效）

直接在 PowerShell 中运行以下两行命令：

PowerShell

```
# 修改控制台输出编码为 UTF-8
[Console]::OutputEncoding = [System.Text.Encoding]::UTF8

# 修改 PowerShell 默认的输出流编码（影响管道符传递）
$OutputEncoding = [System.Text.Encoding]::UTF8
```

**验证：** 再次输入 `[Console]::OutputEncoding`，你应该能看到 `WebName` 变成了 `utf-8`。

---

### 方法 2：永久修改（通过配置文件）

如果你希望每次打开 PowerShell 都是 UTF-8，可以将其写入你的 **PowerShell 配置文件 (Profile)**：

1. **检查配置文件是否存在：**
    
    输入 `test-path $PROFILE`。如果返回 `False`，运行：
    
    `New-Item -Type File -Path $PROFILE -Force`
    
2. **打开配置文件：**
    
    `notepad $PROFILE`
    
3. **将以下内容粘贴并保存：**
    
    PowerShell
    
    ```
    $OutputEncoding = [System.Text.Encoding]::UTF8
    [Console]::OutputEncoding = [System.Text.Encoding]::UTF8
    ```
    
4. **重启 PowerShell** 即可。
    

---

### 方法 3：强制系统全局使用 UTF-8（进阶方案）

如果你发现即使改了 PowerShell，运行 Python 脚本或其他外部工具依然有乱码，可以开启 Windows 的 **Beta: 统一使用 Unicode (UTF-8) 提供全球语言支持**。

1. 按下 `Win + R`，输入 `control` 进入控制台。
    
2. 选择 **区域** -> **管理** 选项卡 -> **更改系统区域设置**。
    
3. 勾选 **“Beta 版：使用 Unicode UTF-8 提供全球语言支持”**。
    
4. **重启电脑**。
    
    _注意：此操作可能会导致部分极其古老的软件出现乱码，但在现代开发环境下通常是首选。_
    

---

### 补充技巧：chcp 命令

你也可以通过传统的命令行方式快速切换：

- 输入 `chcp 65001` 切换到 **UTF-8**。
    
- 输入 `chcp 936` 切换回 **GBK/GB2312**。
    

**建议：** 既然你在学习 Python，强烈建议使用 **方法 2** 修改配置文件，这样能确保你的 Python 打印输出能正确显示中文字符。

需要我帮你写一段测试脚本来验证修改后的中文显示是否正常吗？

## 修改环境变量

```powershell
#仅限当前窗口 
dir env: #查看环境变量
$env:变量名 = "值" #修改环境变量
Remove-Item Env:\变量名 #删除环境变量

#为当前用户设置环境变量
[Environment]::GetEnvironmentVariable("变量名", "User")
[Environment]::SetEnvironmentVariable("变量名", "值", "User")
[Environment]::SetEnvironmentVariable("变量名", $null, "User")
```

## 设置代理

```powershell

$env:HTTP_PROXY = "http://127.0.0.1:10808"
$env:HTTPS_PROXY = "http://127.0.0.1:10808"

```
