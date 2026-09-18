# Surge 配置修改日志

## 2026-09-07 OpenAI 切换到 Surge 内置 Tailscale Oracle 出口

- 用户确认官方核心域名、共享依赖与语音 IP 全部走 Oracle；第二代理已复核方案。
- 修改当前 `s 自定义规则 (RioLU节点).conf`，Finder 备份为同目录 `s 自定义规则 (RioLU节点) copy.conf`，备份含原有凭据，应保持本地私有。
- 保留 `EB731CC8` 身份，在该 Tailscale 段增加 `exit-node = 100.76.246.113` 和 `dns-server = 1.1.1.1, 8.8.8.8`。
- 以官方清单的 8 条域名后缀、12 条精确域名及 23 条语音 IPv4 /32 规则替换旧 OpenAI 远程规则集，放在广告规则之前，保留原有 tailnet、SMTP、LAN 规则优先级。
- 不再使用旧 OpenAI 列表的宽泛 ASN、关键词和整个共享厂商域名匹配；不代表这些旧目标被认定为弃用。
- `surge-cli -c` 返回 `OK`。用户观察到多次 reload，而当前会话工具记录不完整；最终验证阶段未再次执行 reload，直接确认运行态已生效。
- 运行态：`ready`，exitNode `active`，selector `100.76.246.113`，策略 DNS 为 `1.1.1.1`、`8.8.8.8`。
- `test-policy-external-ip tailscale` 返回 `129.150.48.208`；通过本地 HTTP 代理访问 `https://chatgpt.com/cdn-cgi/trace` 返回相同 IP、`loc=SG`、`colo=SIN`。
- `https://api.openai.com/v1/models` 无凭据请求返回 HTTP 401；`test-policy-udp tailscale` 返回 RTT 98 ms；新建 SSH 成功。
- 未验证登录后的对话、Codex、文件上传或实时语音；23 条语音 IP 是官方 JSON 的快照，需要随上游变动刷新。
- 日志显示代理节点域名经 doh.pub 解析失败曾短暂影响 Tailscale 控制连接，随后恢复；未据此修改其他代理/DNS设置，亦未确认重复 reload 来源。
- 独立 Tailscale 扩展仍待下次重启完成移除；未主动重启或使用用户提供的 API 密钥。

## 2026-09-07 核实内置 Tailscale 并卸载独立 App

- 用户要求确认 Surge 内置 Tailscale 的独立性，条件成立后卸载本机独立 App。
- 官方 Surge 文档与独立代理复核确认：Surge 自己注册节点、维护身份和隧道，不依赖独立 App；不等同于独立客户端的入站服务功能。
- 卸载前独立 Tailscale VPN 为 Disconnected，Surge 为 Connected。
- 按 Tailscale 官方 Standalone 卸载方式，通过 Finder 将 `/Applications/Tailscale.app` 移入废纸篓。
- 卸载后应用路径不存在，独立 VPN 配置已移除；扩展状态为 `terminated waiting to uninstall on reboot`，待下次重启完成最终移除。未主动重启。
- 验证 Surge 内置 Tailscale 仍为 `ready`，身份 `100.69.205.4`，Oracle peer 为 direct，新的 SSH 连接成功返回远端 hostname。
- 未清理密钥链或身份数据，未删除远端 Tailscale，未使用用户提供的 API 密钥，未修改 Surge 分流或出口配置。
- 最新 OpenAI 域名、语音地址和 API 弃用证据见 `openai-tailscale-research-2026-09-07.md`。旧清单未列出或新清单不再列出的条目均不能直接认定弃用。

## 2026-09-02 修复 Antigravity "User location is not supported" 错误

### 问题
- Antigravity IDE 报错: `HTTP 400 Bad Request - User location is not supported for the API use`
- Trace header: `X-Cloudaicompanion-Trace-Id` 表明请求涉及 cloudaicompanion.googleapis.com

### 根因
- 现有 ConnersHua Google.list (20条) 未覆盖 Antigravity 核心端点
- `cloudcode-pa.googleapis.com` 和 `cloudaicompanion.googleapis.com` 被 blackmatrix7 Google.list 捕获
- 走 SSRDOG 组（可能为非美国节点），触发 Google location 限制

### 调研来源
- Google 官方文档: Gemini Code Assist 网络配置要求
- opencode-antigravity-auth API 规范文档
- PicoClaw Antigravity 集成文档
- yuaotian/antigravity-proxy 项目（验证相同错误场景）

### 修改内容
文件: `~/Library/Application Support/Surge/Profiles/s 自定义规则 (RioLU节点).conf`

在 `[Rule]` 段 Gemini / Google AI 规则之后插入精确规则:

```surge
# === Antigravity / Cloud Code Assist 精确补充 ===
DOMAIN,cloudcode-pa.googleapis.com,"🇺🇸 United States"
DOMAIN,cloudaicompanion.googleapis.com,"🇺🇸 United States"
DOMAIN,daily-cloudcode-pa.sandbox.googleapis.com,"🇺🇸 United States"
```

### 结果
✅ 已解决 (2026-09-02 确认) — 精确规则足够，无需扩大为 DOMAIN-SUFFIX,googleapis.com

### 验证记录
1. Surge 重载配置后，3 条精确规则生效
2. cloudcode-pa.googleapis.com / cloudaicompanion.googleapis.com 走 🇺🇸 United States 组
3. Antigravity IDE 恢复正常，不再报 "User location is not supported"

### 完整需要代理的域名列表（供参考）
P0: cloudcode-pa.googleapis.com, cloudaicompanion.googleapis.com, oauth2.googleapis.com, accounts.google.com
P1: www.googleapis.com, serviceusage.googleapis.com, cloudresourcemanager.googleapis.com
P2: people.googleapis.com, firebaselogging-pa.googleapis.com, feedback-pa.googleapis.com, apihub.googleapis.com
P3: lh3/lh5.googleusercontent.com, aiplatform.googleapis.com (仅企业版)

## 2026-09-09 同步 RioLU 新订阅服务器（仅服务器更新）

- 订阅：`https://api1.0443.org.uk/RioLU/.../subscribe?token=...`（新 token，需 Surge Mac UA 才返回托管配置；token 不落盘，已清理 /tmp 快照）。
- 目标文件：`~/Library/Application Support/Surge/Profiles/s 自定义规则 (RioLU节点).conf`；备份为同目录 `...conf.backup.20260909_0937`。
- 仅改动 `[Proxy]` 内 RioLU 静态快照（注释日期 2026-08-19 → 2026-09-09）与最小必要的 `[Proxy Group]` 引用；`[General]`、`[Rule]`、`[URL Rewrite]`、`[MITM]`、`[Ponte]`、`[Tailscale EB731CC8]`、VLESS-SMTP、tailscale 均未动。
- 服务器变更：新 UUID `268f53...` 替换旧 `784b...`；中转 `0ec6449b...` → `45f2c5e7...`（港01/02、台家宽03/04、英02、法02、德02、韩02、土02）；日03/04 切到 `78f91345...`；新加坡02切到 `271f2b67...`（sni 同01）、新加坡03 sni 切到 `c4c7...` 并新增 `🇸🇬 新加坡04`（sni `0c54...`）；`支持AI:新日美台港` 改名 `支持AI:新美台日港`；日 CloudFront 换新 sni/host（`d2eaez...`、`d3b9cg...`）；全节点补 `udp-relay=true`（沿用原有空格/引号格式）。
- Proxy Group 最小同步：SSRDOG 跟随改名；`⚡️ Best` 与 `🇸🇬 Singapore` 加入 `🇸🇬 新加坡04`。
- 验证：46 个 RioLU 节点名与订阅一致、参数归一化后零差异；无重名代理；组引用无缺失；Rule/General 等五段与备份逐字一致。无 surge-cli，未执行 reload，需在 Surge 内手动重载生效。

## 2026-09-14 修复 OneDrive 登录入口超时

- 用户网址：`https://onedrive.live.com/about/auth?signin=1&icid=marketing_nav_signin`。
- 原运行记录：Microsoft.list 将 onedrive.live.com 分为 DIRECT；缓存地址 157.240.7.5，反复连接超时。
- 在当前配置 [Rule] 顶部加入 `DOMAIN,onedrive.live.com,SSRDOG`，仅覆盖该域名。
- 原配置备份：同目录 `s 自定义规则 (RioLU节点).conf.backup.20260914_070128.onedrive`（权限 600）。
- surge-cli -c 返回 OK，reload 返回 success；运行记录确认走新加坡代理。
- curl 请求该路径返回微软 403，但真实浏览器访问用户原网址已跳转 /login，成功显示嵌入 odc.officeapps.live.com 的 Sign in、Email or phone 和 Next 表单。未输入账号或测试登录后操作。

## 2026-09-16 Claude / Anthropic 切换 Tailscale

- 修改当前 `s 自定义规则 (RioLU节点).conf`，仅 `[Rule]` 变化；备份为同目录 `s 自定义规则 (RioLU节点).conf.backup.20260916_201827.anthropic-tailscale`（600）。
- 删除原美国组 Claude 规则块；在 OpenAI / 广告拦截之前增加 34 条规则：7 个核心域名后缀、19 个专用/共享目标、Sentry 后缀、原 ConnersHua 动态规则集及 6 条 Claude / claude / Helper 进程兜底，全部指向 tailscale。downloads.claude.ai 由 claude.ai 后缀覆盖。
- 来源：ConnersHua Anthropic.list、blackmatrix7 Claude.list、https://code.claude.com/docs/en/network-config 、https://claude.com/docs/third-party/claude-desktop/telemetry 。官方 Desktop 文档快照与新增规则保存在 `.firecrawl/`。
- 共享 CDN、Google Storage、Datadog、Sentry 等匹配会影响其他应用；未将整个 Google/AWS/Azure 网络改走 Tailscale。进程规则保留原局域网/SMTP优先级；任意子进程、第三方 MCP 以及远端执行环境并非都可由本机专属进程规则识别。
- `surge-cli -c` 返回 OK，执行一次 reload 返回 success；运行态规则确认旧 Claude 美国组条目已清除。
- 实际请求 claude.ai、api.anthropic.com、downloads.claude.ai 的 policyName 均为 tailscale，命中对应 DOMAIN-SUFFIX，使用 WireGuard 远程 DNS。
- Claude trace 返回出口 129.150.48.208、loc=SG，与 tailscale 出口测试一致。API 无凭据请求 HTTP 401；下载域名根路径 HTTP 403，不代表具体安装文件下载验证成功。
- 未进行登录后对话、Cowork、上传/下载或所有进程兜底实测；覆盖已核对的域名和可识别专属进程，不声称穷尽未来新增域名及任意外部工具流量。
