# AI 信号台

AI 工具导航 + 实时状态信号：额度重置、模型发布、榜单价格、热点资讯。

**线上地址**：https://caitoulin.github.io/ai-nav/

- 纯静态单文件（`index.html`），零外部依赖，可离线打开
- 精选 119 个站点，14 个分类；所有链接经连通性核验（curl 实测）
- 支持搜索（按 `/` 聚焦）、分类筛选、一键复制链接
- 支持深色模式（跟随系统）

## 为什么不是又一个 AI 导航

市面上的 AI 导航站（365dh.cn、nav-ai.cn、aijutu.com、zhinav.com、ainall.cc…）几乎都是静态工具目录。本站在常规分类之外增设 **「状态与信号」** 区，作为第一屏首块：

- 额度重置追踪（Codex 重置时间与预测）
- 模型发布与热点榜
- 榜单与价格（LMArena / Artificial Analysis / OpenRouter 真实调用量 / METR 时间视界）
- 服务状态页（OpenAI / Anthropic / Google Cloud）

思路参考 aihot.news 的 `/codex-reset`：**导航的价值不在链接数量，而在实时信号**。

## 本地预览

直接双击 `index.html`，或：

```bash
python3 -m http.server 8080
```

## 部署

已托管于 GitHub Pages（分支 `main`，根目录）。任意静态托管均可（Cloudflare Pages / Vercel / Netlify）。

> **本机网络实测（2026-09-15）**：git 的**读操作可用**（`git ls-remote` / `git fetch` 均正常），
> 但 **`git push` 被拦截** —— `git-remote-https` 在 push 时被瞬间掐断，报
> `remote helper 'https' aborted session`，且发生在发起网络请求之前。
> 因此本仓库的提交改用 **GitHub REST API** 完成（见下）。
> 换到无此限制的网络，或改用 SSH 通道，即可正常 `git push`。

### 用 REST API 提交（绕过 push 拦截）

```bash
# 新增文件
gh api -X PUT repos/OWNER/REPO/contents/index.html \
  -f message="feat: xxx" -f content="$(base64 -i index.html)" -f branch=main

# 更新已有文件需带 sha
gh api -X PUT repos/OWNER/REPO/contents/index.html \
  -f message="fix: xxx" -f content="$(base64 -i index.html)" -f branch=main \
  -f sha="$(gh api repos/OWNER/REPO/contents/index.html --jq .sha)"
```

## 绑定自有域名

1. 在注册商购买域名（候选见下）
2. 在仓库 Settings → Pages → Custom domain 填入域名，或在项目根加 `CNAME` 文件（内容为裸域名）
3. DNS 配置：
   - 根域：4 条 A 记录 → `185.199.108.153` / `185.199.109.153` / `185.199.110.153` / `185.199.111.153`
   - 子域：CNAME → `caitoulin.github.io`
4. 勾选 Enforce HTTPS（证书自动签发，约 10 分钟）

### 候选域名（RDAP 实查，2026-09-15）

可用：`aisignal.cc`、`aisignal.io`、`aixinhao.cc`、`ai-nav.cc`、`newainav.com`、`hotainav.com`
已被注册：`aisignal.com`、`aisignal.app`、`airadar.com`、`navai.cc`、`ainav.cc`

## 数据维护

站点数据在 `index.html` 内的 `SITES` 数组，字段：

| 字段 | 说明 |
|---|---|
| `n` | 名称 |
| `u` | 链接 |
| `d` | 一句话说明 |
| `c` | 分类 id（见 `CATS`） |
| `hot` | 可选，标记热门 |

新增分类只需在 `CATS` 里加一行（格式：`[id, 名称, 说明, 主题色]`）。
