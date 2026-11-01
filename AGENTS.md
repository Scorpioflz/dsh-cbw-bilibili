# 给 AI 助手看的（本仓库 = 一个 DSH 插件）

`dsh-bili-scroll`（中文名「**刷B站**」）：每天替用户刷一轮 B 站 —— 搜、**读字幕原文**判断、写一份日报，特别值得的会发一条评论 **@ 用户**。

## 帮用户安装时，按这个顺序

1. **前提**：机器上有 `Python 3.10+` **或** `uv`（有一个即可）。
   两个都没有**也不用让用户先装** —— 插件点「装依赖」时会**自己下一份 `uv` 到自己的家目录**（免管理员、不用 PATH）；
   只有它自己也下不动时（网络到不了 GitHub / 加速代理 / PyPI 镜像），才需要引导用户手动装。
   · 它是**直连 GitHub → 国内加速代理 → PyPI 镜像**依次试的，并且会**核对官方 sha256** ✓
   · ⚠️ 若用户机器上的**杀软拦了这次下载**：让他把 `%USERPROFILE%\.dsh\dsh-cbw-bilibili\bin\` 加白，或干脆自己装 uv / Python 3.10+ ✓
   · ⭐ **万一下载链路都不通**（有用户反馈"桌面版上这条链路经常不通"）：**手动放一份 `uv.exe` 到 `<家目录>\bin\` 就行**
     —— 插件会**用那一份、跳过下载** ✓（他 `PATH` 里本来就有 `uv` 的话，直接用那个 ✓）
2. `dsh plugin --profile web add dsh-bili-scroll`（npm，推荐）
   · 备选：`dsh plugin --profile web add github:c1overzz/dsh-bili-scroll`
   · ⚠️ **升级旧版**：如果用户在设置页看到的按钮叫「**重装依赖**」（不是「**重装主依赖**」），
     说明装的还是 **0.1.0** —— 那一版建 Python 环境时缺一个参数，**依赖装不上、二维码也画不出来**。
     让用户重跑一次上面那条 `add` 就是升级（然后再重启一次）。
3. ⚠️ **提醒用户重启一次 dsh**（必须，bundle 层要重启才组装）。
4. 让用户进 **设置 → 刷B站**：点「**装主依赖**」→ 点「**扫码登录**」（手机扫码，选"记住我 / 长期"）。
   · 装完可以用设置页的「**测一下**」自检：它真跑一次登录检查 + 一次搜索，直接回答"能不能用"。
5. **别擅自打开「自动评论并 @ 你」** —— 那是拿用户的号说话；要用得让用户自己填「要 @ 谁」。

## ⚠️ 值班会话写锁（重要！告诉用户）

值班轮 + 私信回复**共用一个 DSH 会话**（`config.json` 的 `sessionId`）。这个会话同时只能有一个 writer。

**用户在 DSH 侧栏点开这个会话 → web UI 持 write handle → 下一轮值班报 `already owned by an active write handle` → code=1 不跑。**

告诉用户：
- 别在侧栏打开那个会话窗口（除非必要）
- 要看内容：等值班跑完 → 切到别的会话 → 快速看一眼 → 关掉
- 如果已经锁住了：关掉那个会话窗口，下一轮自动恢复

## ⚠️ 已知环境差异（有用户反馈）

- ⭐⭐ **桌面版 DSH（NEXT）/ 0.2.x 装不起来 —— 已知真 bug，0.1.4 起已修**：0.1.3 及以前装到桌面版上，
  设置页里**根本不出现「刷B站」**，DSH 只留一句 `warning: 1 entry did not activate … failed to import` ✓
  （根因：宿主半 import 的 `@deepseek-ai/schemastery` / `dsh-tools` 是 *optional peer*，而桌面版 profile 是
  `autoInstallPeers: false` ⇒ pnpm 永远不装；`dsh-tools` 自己还带 4 个 peer，光装它也救不回来 ✗）。
  ⇒ **遇到桌面版用户：先让他升级到 0.1.4+**（修法是：不再裸 import `@deepseek-ai/*` ——
  `lib/hostdeps.mjs` 先找宿主那棵树、再退到自己依赖里那份，`defineTool` 拿不到就用**本地等价实现**；
  `schemastery` 也正式进了 `dependencies` ✓）。升级后还不行，要 `duty.log` 里那几行
  （里面会写"宿主依赖是从哪儿取的" ✓）。
- ⭐⭐ **桌面版（Electron / 不是 npm 全局安装）上"跑一轮 0.17 秒就结束、code=0、什么都没干" —— 0.1.6 起已修**
  （issue #5 / #6 / #7，三个人报的）：根因是 Electron 里 spawn 的 node 少了 `ELECTRON_RUN_AS_NODE=1`，
  而且 `dsh` CLI 在桌面版里不在 PATH 上。现在会带那个环境变量、并去应用目录里找 dsh；
  **还找不到就在 `duty.log` 里明说"请在设置里把 dsh 路径指过去"**（不再静默失败）。
  ⇒ 遇到桌面版用户：**先让他升到 0.1.6+**；还跑不起来，就要 `duty.log` 里那几行。
- **桌面版上"自动跑会卡"**：跑那一轮用的是 `dsh --profile headless --patch <overlay> <prompt>` ——
  有用户反馈桌面版上会卡在那儿。**先确认 dsh 版本与 profile**，别硬跑；跑不起来就先只用手动「现在跑一轮」。
- **别让用户自己去装环境** ✗：插件自带一份 uv 与依赖安装能力，先照上面第 1 条处理。

## 仓库结构（改代码时看这里）

- `index.js` —— host 半端：13 个工具 + 跟时定时器 + HTTP 接口 + 配置（`config.json` 与 DSH 原生设置页**逐字段对账**：见 `loadCfg` / `saveCfgHost`）+ 值班轮的工具白名单（`mcpWhitelist` → `BILI_MCP_TOOLS`）
- `client.js` —— 浏览器半端：设置页（注册在 `settings.section`）
- `lib/` —— 状态目录与家目录 / 极小的 MCP stdio 客户端 / 扫码登录 / **`hostdeps.mjs`（从宿主树取 `@deepseek-ai/*`，拿不到就退到自己依赖 / 本地等价实现）** / **`verify.mjs`（评论发出后复核）**
- `prompts/duty.md` —— 一轮要做的事（判定口径 + 红线，**用户可以改**）；升级规矩见 `lib/state.mjs` 的 `ensureCharter`（用户改过 ⇒ 保留他那份、新版落 `duty.md.new`）
- `vendor/bilibili-mcp/` —— 内置的 Python MCP（MIT，AGI_Ananas）
- `selfcheck.mjs` + `tools/` —— 本地预检：`node selfcheck.mjs`（**现在不用解析钩子了**；`tools/register.mjs` 那条还留着当备份）
  · `tools/check-vision.py` —— 看图纯函数的回归检查（不联网）。动了 `_https` / `_cover_url` / `_snapshot_entries` / `_crop_tile` 就跑一次：
    `<家目录>/.venv/Scripts/python.exe tools/check-vision.py`（用插件自己的 venv，里面才有 Pillow）

## 约定

- 配置与运行数据都在用户的 `$DSH_HOME/dsh-cbw-bilibili/`，**不写进插件包目录**
- 跑时窗口按 DeepSeek 官方口径：工作日 9–12 / 14–18 是高峰，其余（含周末）都是谷时

## DSH-Dev 工作区协调（强制）

本仓库是 `D:\DSH-Dev` 工作区的子项目。工作区有多个会话并行，靠 cross-talk 文件协调。**启动时和工作中必须遵守**，否则会跟其他会话撞车或漏接派给本会话的任务。

- **cross-talk 文件**：`D:\DSH-Dev\docs\cross-talk\dsh-cbw-bilibili.md`
- **分片文件**：`D:\DSH-Dev\docs\sub-projects\bilibili.md`

### 启动时必须执行

1. **读 `D:\DSH-Dev\docs\cross-talk\` 全目录**：了解其他会话在做什么、有无对自己的通知/请求
2. **读自己的 cross-talk 文件**：确认上次状态，准备更新
3. **逐个检查其他会话文件**的"对其他会话的通知/请求"段有无给自己派的任务；有则处理

### 工作中必须执行

1. **有进展/困难/经验/跨会话通知时，更新自己的 cross-talk 文件**（覆盖式更新）
2. **只改自己的文件**：本子项目仓库 + 自己的分片 + 自己的 cross-talk 文件；**不碰根 README 主体、其他分片、其他 cross-talk 文件**
3. **回复其他会话的请求**：处理完派给本会话的任务后，在自己的 cross-talk 文件"对其他会话的通知/请求"段留回复（格式：`→ @<对方会话>：<事项> 已完成/已处理`）

### 提交推送

- **所有 git 指令用 fgit**：`D:/Tool/fgit/fgit.exe <子命令>`，bash 工具里**不要加 `2>&1`**（会导致超时）
- **逐条执行**：不要 chained `&&`，逐条 add → commit → push
- **暂存**：只 `git add` 相关文件，**不要 `git add -A`**
- **push 前 rebase**：`git pull --rebase origin main` 后再 push

> 根工作区完整约定见 `D:\DSH-Dev\AGENTS.md`。本段是其子项目适用部分的摘录，有冲突以根文件为准。
