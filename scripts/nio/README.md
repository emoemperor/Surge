# 蔚来自动签到（Surge）

本目录为蔚来 App 的自动签到脚本，当前实现包含：

- 抓取并保存请求头中的 `authorization`
- 定时调用签到接口
- 通知签到结果与连续签到天数
- 对 token 做时效校验（超过 7 天会提示重新抓取）

## 文件说明

- `nio.sgmodule`：Surge 模块入口（脚本任务 + MITM）
- `cookie.js`：抓取并更新 token
- `check_in.js`：执行签到
- `draft.js`：调试草稿

## 使用方式

### 1. 导入模块

在 Surge 模块中添加：

```ini
https://raw.githubusercontent.com/emoemperor/Surge/main/scripts/nio/nio.sgmodule
```

### 2. 抓取 token

当前 `cookie.js` 的拦截规则为：

```text
https://app.nio.com/app/api/social/v2/im/account?... 
```

操作步骤：

1. 确认模块已启用。
2. 打开蔚来 App 并进入会触发上述接口的页面。
3. 看到 `✅更新cookie` 通知即表示抓取成功。

保存键如下：

- `emo_nio_token`
- `emo_nio_token_update_at`

### 3. 自动签到

`check_in.js` 默认 cron：

```cron
0 7 * * *
```

即每天 07:00 自动签到，调用接口：

```text
https://gateway-front-external.nio.com/moat/10086/c/award_cn/checkin
```

## 运行逻辑说明

- 当未抓取到 token，或 token 更新时间超过 7 天时，脚本会提示 `⚠️请先获取cookie`。
- 签到成功后会展示 `result.data.stats.continuous_checkin_days` 与 `tip`。
- 若接口返回失败，会在通知中显示失败状态和返回码。

## 常见问题

### 1. 一直提示先获取 cookie

- 重新打开蔚来 App，确保触发了 `im/account` 这个请求。
- 检查 Surge 中 `蔚来签到Token获取` 规则是否生效。
- 如果长时间未更新 token（超过 7 天），需重新抓取。
- 若仍抓取不到，可在 MITM `hostname` 中额外加入 `app.nio.com`。

### 2. 抓取成功但签到失败

- token 可能已失效，重新抓取。
- 蔚来接口字段可能变更，可参考 `draft.js` 调整。

## 免责声明

本脚本仅用于学习与自动化实践，请在遵守平台条款和当地法律法规的前提下使用。
