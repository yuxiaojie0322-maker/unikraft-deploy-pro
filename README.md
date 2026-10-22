# unikraft-deploy (增强优化版)

在 [Unikraft Cloud](https://unikraft.cloud) 上部署高性能 VLESS over WebSocket 代理服务。

已深度优化：
- 🚀 **多账号矩阵支持**：单个仓库无缝调度 3 个 Unikraft 账号（支持最多 6 个并发免费节点）
- ⚡ **油管 4K 秒开优化**：内置 0-RTT WS 早期数据 (`ed=2048`)、Google DNS 直连、`prefer_ipv4` 防验证码
- 🛡️ **双通道保护**：原生 Unikraft 直连 + Cloudflare Argo Tunnel（内置自动优选域名 CleanIP）
- 📉 **配额保护**：内存精简至 `128M`（省 75% 免费额度），部署自动清理同地区旧实例防超限

---

## GitHub 配置

在你的仓库 **Settings → Secrets and variables → Actions** 中配置以下 Secrets：

| Secret | 必填 | 说明 |
| :--- | :---: | :--- |
| `UUID` | **是** | VLESS 节点密码，兼作网页端解密密钥 |
| `UNIKRAFT_TOKEN_A` | 可选* | 账号 A 的 Unikraft API Key |
| `UNIKRAFT_TOKEN_B` | 可选* | 账号 B 的 Unikraft API Key |
| `UNIKRAFT_TOKEN_C` | 可选* | 账号 C 的 Unikraft API Key |
| `UNIKRAFT_TOKEN` | 可选* | 默认账号 Token（单账号用户只填这个即可） |
| `ARGO_DOMAIN` | 否 | Cloudflare Tunnel 域名（如 `proxy.yourdomain.com`） |
| `ARGO_TOKEN` | 否 | Cloudflare Zero Trust Tunnel Token |

> 注：Argo 参数需同时填写。在 Cloudflare Zero Trust 后台添加 Public Hostname 时，Service 协议选择 `HTTP`，URL 填 `127.0.0.1:8080`。

---

## 3 个账号的 6 节点推荐布局

| 账号 | 建议部署节点 1 | 建议部署节点 2 | 用途说明 |
| :---: | :---: | :---: | :--- |
| **账号 A** | `sin`（新加坡 1） | `sfo`（旧金山 1） | 主力高速组：日常 4K 刷油管、看视频首选 |
| **账号 B** | `sin`（新加坡 2） | `fra`（法兰克福） | 亚洲备用 + 欧洲覆盖：双机热备，防单点故障 |
| **账号 C** | `was`（华盛顿） | `dal`（达拉斯） | 美洲深度覆盖：美区流媒体与 AI 工具冗余 |

---

## 部署步骤

1. 打开仓库 **Actions → Create Unikraft Instance**
2. 点击 **Run workflow**：
   - **选择部署账号**：账号 A / B / C 或默认
   - **选择地区**：`sin` / `sfo` / `fra` / `was` / `dal`
   - **内存规格**：默认 `128M`（单机推荐 128M，节省配额）
   - **自动清理旧实例**：默认勾选（自动清理同地区旧实例，防止超出 2 台免费配额）
3. 运行完成后，在 **Summary** 页面点击解密链接，输入 `UUID` 即可获得节点订阅。

---

## 油管 4K 秒开关键设置

1. **禁用浏览器 QUIC**：
   在 Chrome / Edge 打开 `chrome://flags/#enable-quic`，设置为 **Disabled** 并重启浏览器。强制走 TCP，彻底杜绝起播转圈和拖拽进度条卡顿！
2. **客户端开启 Mux（多路复用）**：
   在代理客户端（如 v2rayN / Clash Verge / Sing-box）中开启 Mux 多路复用，并发合并拉取视频切片。
3. **Argo 节点优选 IP**：
   订阅中已内置 `Unikraft-Argo-CleanIP` 节点，将流量导向延迟最低的 Cloudflare 节点。
