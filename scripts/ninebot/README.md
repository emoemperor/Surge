# 九号出行自动签到（Surge）

本目录提供九号出行自动签到脚本，包含以下能力：

- 抓取并保存 `authorization` 与 `device_id`
- 每日定时调用签到接口
- 通过系统通知返回签到结果

## 文件说明

- `ninebot.sgmodule`：Surge 模块配置（包含脚本任务与 MITM）
- `cookie.js`：请求拦截脚本，用于保存登录凭据
- `check_in.js`：定时签到脚本
- `logo.png`：模块图标

## 使用方式

### 1. 在 Surge 中导入模块

可直接在 Surge 模块中添加：

```ini
https://raw.githubusercontent.com/emoemperor/Surge/master/scripts/ninebot/ninebot.sgmodule
```

如果你是本地调试，也可以手动将 `ninebot.sgmodule` 内容加入现有配置。

### 2. 抓取 Cookie 与 Device ID

`cookie.js` 监听以下请求：

```text
https://cn-cbu-gateway.ninebot.com/portal/api/user-sign/v2/sign
```

操作步骤：

1. 确认 Surge 已开启并启用模块。
2. 打开九号出行 App（或 H5）进入签到页面并触发一次签到请求。
3. 看到通知 `更新cookie和device_id成功` 即表示抓取完成。

脚本会将数据保存到持久化存储：

- `emo_ninebot_cookie`
- `emo_ninebot_device_id`

### 3. 自动签到

`check_in.js` 默认 cron：

```cron
0 7 * * *
```

即每天 07:00 自动执行签到。

## 通知说明

- `✅签到成功`：已完成签到，可能会显示奖励信息
- `⚠️请先获取cookie和device_id`：未抓取到凭据，先执行上面的抓取步骤
- `❌签到失败`：接口返回失败或网络异常

## 常见问题

### 1. 一直提示未获取 cookie

请检查：

- 模块是否启用
- `MITM` 中是否包含 `cn-cbu-gateway.ninebot.com`
- 是否确实触发了 `/portal/api/user-sign/v2/sign` 请求

### 2. 抓取成功后过几天失效

`authorization` 可能过期。重新进入签到页面触发一次请求即可更新。

## 免责声明

本脚本仅供学习与自动化实践使用，请在遵守平台条款和当地法律法规的前提下使用。
