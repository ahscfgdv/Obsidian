`https://docs.openclaw.ai/`

## 安装

通过pnpm安装节省空间

```powershell
OpenClaw 2026.4.1 (da64a97)
pnpm add -g openclaw@latest
pnpm approve-builds -g
openclaw onboard --install-daemon

pnpm add -g grammy #安装缺失的包
pnpm add -g @aws-sdk/client-bedrock @aws-sdk/client-bedrock-runtime
pnpm add -g @buape/carbon
```

## Command

```powershell
openclaw gateway #运行网关
openclaw gateway restart #重启网关

openclaw update #升级openclaw 推荐使用管理员执行
```