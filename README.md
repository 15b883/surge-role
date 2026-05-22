# surge-role

Personal Surge 6 rule sets, modules, and configuration template.

## File Structure

```
surge-role/
├── surge.conf                    # Main configuration template (Surge 6)
├── rules/
│   ├── ai.list                   # AI services (OpenAI, Claude, Gemini, etc.)
│   ├── apple.list                # Apple services (iCloud, App Store, etc.)
│   ├── direct.list               # Domestic services (DIRECT)
│   ├── microsoft.list            # Microsoft services (Office 365, OneDrive, etc.)
│   ├── proxy.list                # Services requiring proxy (Google, GitHub, Twitter, etc.)
│   ├── reject.list               # Ad / tracker block rules
│   ├── streaming.list            # Overseas streaming (Netflix, Disney+, TikTok, etc.)
│   └── tencent.list              # Tencent services (WeChat, QQ, etc.)
└── modules/
    ├── panel/
    │   ├── IP-Check.sgmodule     # Node IP / ISP / location panel
    │   ├── Network-Info.sgmodule # Network interface & connection details panel
    │   ├── Sub-Info.sgmodule     # Subscription traffic & expiry panel
    │   ├── Stream-Unlock.sgmodule# Netflix/Disney+/YouTube unlock detection panel
    │   ├── Flush-DNS.sgmodule    # DNS cache flush + latency panel
    │   └── Surge-Pro.sgmodule    # Surge license & version info panel
    └── spec/
        ├── Google-Redirect.sgmodule  # Redirect google.cn to google.com
        ├── TestFlight-Unlock.sgmodule# Fix TestFlight other-region "app unavailable"
        ├── Sub-Store.sgmodule        # Advanced subscription manager
        ├── Bilibili-Purify.sgmodule  # Bilibili iOS app ad removal
        ├── Weibo-Purify.sgmodule     # Weibo iOS app ad removal
        ├── HTTPS-Upgrade.sgmodule    # Auto-upgrade HTTP to HTTPS
        ├── Ad-Block-General.sgmodule # URL-pattern ad blocker
        ├── DoH-AliDNS.sgmodule       # DNS over HTTPS via AliDNS
        └── DoH-DNSPod.sgmodule       # DNS over HTTPS via DNSPod
```

## Quick Start

### 方案一：单节点（无订阅链接）

适合只有一个自建节点的用户。

**第一步 — 在 `[Proxy]` 节填写节点信息**

根据协议选择对应格式：

```ini
# Trojan
US-Node = trojan, 1.2.3.4, 443, password=your_password, skip-cert-verify=false

# Shadowsocks
US-Node = ss, 1.2.3.4, 8388, encrypt-method=chacha20-ietf-poly1305, password=your_password

# VMess
US-Node = vmess, 1.2.3.4, 443, username=your-uuid, ws=true, ws-path=/path

# VLESS
US-Node = vless, 1.2.3.4, 443, username=your-uuid, skip-cert-verify=false
```

**第二步 — 在 `🇺🇸 美国节点` 策略组填写节点名**

将 `YOUR-US-NODE-NAME` 替换为上面填写的节点名（如 `US-Node`）：

```ini
🇺🇸 美国节点 = select, US-Node, icon-url=...
```

**第三步 — 导入 Surge 6**

- iOS：Surge → 配置 → 从 URL 下载配置
- macOS：菜单栏 Surge → 配置 → 从 URL 安装

填入：
```
https://raw.githubusercontent.com/15b883/surge-role/main/surge.conf
```

---

### 方案二：机场订阅（多节点）

适合使用机场订阅链接的用户。将 `surge.conf` 中 `✈️ 全部节点` 行的 `policy-path=` 替换为你的订阅地址，然后按方案一第三步导入即可。

---

3. In Surge → Modules, install any modules you need from the URLs in `[Module]` section

## Installing Modules

Modules are loaded separately from the main config. Copy the URL of the `.sgmodule` file and install it in **Surge → Modules → Install from URL**.

| Module | URL |
|--------|-----|
| IP Check | `https://raw.githubusercontent.com/15b883/surge-role/main/modules/panel/IP-Check.sgmodule` |
| Network Info | `https://raw.githubusercontent.com/15b883/surge-role/main/modules/panel/Network-Info.sgmodule` |
| Sub Info | `https://raw.githubusercontent.com/15b883/surge-role/main/modules/panel/Sub-Info.sgmodule` |
| Stream Unlock | `https://raw.githubusercontent.com/15b883/surge-role/main/modules/panel/Stream-Unlock.sgmodule` |
| Flush DNS | `https://raw.githubusercontent.com/15b883/surge-role/main/modules/panel/Flush-DNS.sgmodule` |
| Google Redirect | `https://raw.githubusercontent.com/15b883/surge-role/main/modules/spec/Google-Redirect.sgmodule` |
| TestFlight Unlock | `https://raw.githubusercontent.com/15b883/surge-role/main/modules/spec/TestFlight-Unlock.sgmodule` |
| Sub-Store | `https://raw.githubusercontent.com/15b883/surge-role/main/modules/spec/Sub-Store.sgmodule` |
| Bilibili Purify | `https://raw.githubusercontent.com/15b883/surge-role/main/modules/spec/Bilibili-Purify.sgmodule` |
| Weibo Purify | `https://raw.githubusercontent.com/15b883/surge-role/main/modules/spec/Weibo-Purify.sgmodule` |
| DoH AliDNS | `https://raw.githubusercontent.com/15b883/surge-role/main/modules/spec/DoH-AliDNS.sgmodule` |

## Rule Priority (top to bottom)

1. Reject (ads / trackers)
2. AI services → AI proxy group
3. Apple services → Apple group (default: DIRECT)
4. Microsoft services → Microsoft group (default: DIRECT)
5. Streaming media → Streaming group
6. Bilibili → Bilibili group (default: DIRECT)
7. Tencent (WeChat/QQ) → DIRECT
8. Proxy rules → Main selector
9. Domestic direct → DIRECT
10. LAN → DIRECT
11. FINAL → Main selector

## Surge 6 Features Used

- `smart` proxy groups — automatic latency-based node selection
- `RULE-SET` with remote rule lists
- `IP-CIDR6` for IPv6 Telegram ranges
- `udp-policy-not-supported-behaviour = reject` — prevents UDP leaks
- `[Sniff]` section — TLS SNI / HTTP Host sniffing
- `proxy-test-udp` — UDP connectivity testing
- `[Module]` section — modular feature loading

## References

- [Rabbit-Spec/Surge](https://github.com/Rabbit-Spec/Surge) — upstream reference
- [Surge Documentation](https://manual.nssurge.com/)

## Credits

本项目引用了以下作者的规则、脚本和模块，在此表示感谢：

### 规则集 / Rule Lists

| 作者 | 项目 | 引用内容 |
|------|------|---------|
| [Rabbit-Spec](https://github.com/Rabbit-Spec) | [Surge](https://github.com/Rabbit-Spec/Surge) | 规则集结构参考、Panel/Script 模块全套 |
| [Hackl0us](https://github.com/Hackl0us) | [GeoIP2-CN](https://github.com/Hackl0us/GeoIP2-CN) | CN GeoIP 数据库（`geoip-maxmind-url`） |

### Panel 面板脚本

| 作者 | 项目 | 用于模块 |
|------|------|---------|
| [congcong0806](https://github.com/congcong0806) | [surge-list](https://github.com/congcong0806/surge-list) | IP-Check 脚本原作者 |
| [Nebulosa-Cat](https://github.com/Nebulosa-Cat) | — | Network-Info 脚本原作者 |
| [mieqq](https://github.com/mieqq) | [mieqq](https://github.com/mieqq/mieqq) | Sub-Info 订阅信息脚本原作者 |
| [zZPiglet](https://github.com/zZPiglet) | [Task](https://github.com/zZPiglet/Task) | Flush-DNS 脚本原作者 |
| [Rabbit-Spec](https://github.com/Rabbit-Spec) | [Surge](https://github.com/Rabbit-Spec/Surge) | Stream-All 流媒体检测、SurgePro、各脚本二次修改 |

### 功能模块脚本

| 作者 | 项目 | 用于模块 |
|------|------|---------|
| [app2smile](https://github.com/app2smile) | [rules](https://github.com/app2smile/rules) | Bilibili-Purify 净化脚本 |
| [Rabbit-Spec](https://github.com/Rabbit-Spec) | [Surge](https://github.com/Rabbit-Spec/Surge) | Weibo-Purify 净化脚本 |
| [nan.ge](https://hub.nan.ge) | — | Weibo Timeline 净化脚本 |
| [NobyDa](https://github.com/NobyDa) | [Script](https://github.com/NobyDa/Script) | TestFlight-Unlock 脚本原作者 |
| [blackmatrix7](https://github.com/blackmatrix7) | [ios_rule_script](https://github.com/blackmatrix7/ios_rule_script) | TestFlight-Unlock 脚本 |
| [Peng-YM](https://github.com/Peng-YM) | [Sub-Store](https://github.com/sub-store-org/Sub-Store) | Sub-Store 订阅管理器 |

---

感谢 [Claude](https://claude.ai) 协助完成本项目的构建与优化。
