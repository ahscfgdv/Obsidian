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

