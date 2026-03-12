# 华住自动签到（Surge）

本目录提供华住会自动签到脚本，包含以下能力：

- 抓取并保存请求头中的 `cookie`
- 每日定时调用华住签到接口
- 通过系统通知返回签到结果（积分与连续签到天数）

## 文件说明

- `huazhu.sgmodule`：Surge 模块配置（脚本任务 + MITM）
- `cookie.js`：请求拦截脚本，用于抓取并保存 cookie
- `check_in.js`：定时签到脚本
- `logo.png`：模块图标

## 使用方式

### 1. 在 Surge 中导入模块

在 Surge 模块中添加：

```ini
https://raw.githubusercontent.com/emoemperor/Surge/main/scripts/huazhu/huazhu.sgmodule
```

### 2. 抓取 Cookie

`cookie.js` 监听以下请求：

```text
https://appgw.huazhu.com/game/play_entry...
```

操作步骤：

1. 确认 Surge 已开启并启用模块。
2. 打开华住会小程序或相关签到页面，触发一次 `play_entry` 请求。
3. 看到通知 `✅更新cookie` 即表示抓取成功。

脚本会将 cookie 保存到持久化存储键：

- `emo_huazhu_token`

### 3. 自动签到

`check_in.js` 默认 cron：

```cron
0 7 * * *
```

即每天 07:00 自动执行签到，请求接口：

```text
https://appgw.huazhu.com/game/sign_in
```

## 通知说明

- `签到成功`：通常会显示本次获得积分与连续签到天数
- `⚠️请先获取cookie`：未抓取到 cookie，请先执行上面的抓取步骤
- `签到失败`：接口返回失败或响应解析失败

## 常见问题

### 1. 一直提示请先获取 cookie

请检查：

- 模块是否启用
- `MITM` 中是否包含 `appgw.huazhu.com`
- 是否确实触发了 `/game/play_entry` 请求

### 2. 抓取成功但签到失败

- cookie 可能已失效，重新进入页面触发一次请求即可更新
- 接口字段可能发生变化，可根据返回内容调整脚本

## 免责声明

本脚本仅供学习与自动化实践使用，请在遵守平台条款和当地法律法规的前提下使用。