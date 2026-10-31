# @dsh-cbw/bilibili

<p align="center">
  <a href="./README.md">English</a> | <a href="./README_ZH.md">中文</a>
</p>

DeepSeek Harness (DSH) 插件：每天替你刷一轮 B站 —— 搜视频、读字幕判断值不值得、写一份日报，值得的就 @ 你。Fork 自 [`dsh-bili-scroll`](https://github.com/c1overzz/dsh-bili-scroll) v2.3.1，新增：固定会话延续、主人画像分析、爱好画像、兴趣学习（半衰期淡忘）、分镜图、评论安全闸门。

## 功能

### 继承自上游（dsh-bili-scroll v2.3.1）
- **值班轮** — 峰谷调度，搜 B站、读字幕、写日报
- **13 个宿主工具** — `bili_whoami`、`bili_search`、`bili_subtitle`、`bili_comments`、`bili_cover`、`bili_frames`、`bili_reply`、`bili_status`、`bili_run`、`bili_login_start/poll`、`bili_setup`、`bili_config`
- **MCP 值班轮工具** — 搜索/推荐、字幕/评论/弹幕、封面/帧、回复/私信、转写
- **私信监听** — 监听主人私信，带会话延续回复
- **自动收藏** — 宿主侧 `favops.py`
- **可指定模型** — `modelProvider`/`modelName` 按轮指定
- **超时看门狗** — `runTimeoutMinutes`（默认 20）

### 本 fork 新增
- **固定会话延续** — 值班轮通过 dsh-web-service REST/SSE 复用固定 `sessionId`，而非一次性 CLI spawn，跨轮保持完整对话上下文。web-service 不可用时自动回退到 CLI spawn。
- **主人画像与爱好分析** — `masterUid` 识别主人；`bili_analyze_hobby` 分析公开收藏夹 + 关注列表 → 爱好画像 → 写入 @dsh-cbw/memory 作为长期事实。刷新周期可配（`hobbyRefreshDays`，默认 30 天）。
- **身份解耦** — `masterUid`（主人，UID）vs `mentionNames`（评论 @ 清单，昵称，可多个）vs `dmRecipients`（私信收件人，可多个）。朋友也能加入分享推荐，不必是主人。
- **兴趣学习 + 半衰期淡忘** — 替代死配置 `keywords`；从值班轮挑的视频自动提取兴趣词，按半衰期淡忘。*[借鉴 bilihave/dsh-whale-panel]*
- **分镜图** — 多格拼一张替代单帧，token 效率更高。*[借鉴 QiuYeDx/dsh-bili-eyes]*
- **评论三层安全闸门** — 政治/政府/前沿科学硬禁区。*[借鉴 bilihave/dsh-whale-panel]*
- **观看统计报告** — 日报加入完成度、时间分布等统计维度。*[借鉴 apiaoapiao/dsh-bili-statistics]*
- **收件箱自动分类** — 回复/@/私信分类处理。*[借鉴 QiuYeDx/dsh-bili-eyes]*

## 派生声明

本插件**直接 fork** 自 [`c1overzz/dsh-bili-scroll`](https://github.com/c1overzz/dsh-bili-scroll) v2.3.1（MIT License）。保留 `upstream` git remote 用于同步上游更新：

```bash
git fetch upstream
git merge upstream/main
```

上游全部代码（`index.js`、`client.js`、`lib/`、`prompts/`、`vendor/`、`profile-template/`）均继承，改动在其上叠加。

## 依赖

### 功能依赖（新增功能需要）
- **[dsh-web-service](https://github.com/toddpan/dsh-webapi)** — 固定会话延续使用其 REST/SSE 会话 API。自动探测 `127.0.0.1:3080`（可通过 `webapiBase` 配置）。不可用时插件自动回退到一次性 CLI spawn（上游行为）。
- **[@dsh-cbw/memory](https://github.com/Scorpioflz/dsh-cbw-memory)** — 爱好画像和兴趣词作为长期事实存储。可选但推荐；不装则画像仅本地存于 `hobby.json`。

### 内置
- **[bilibili-mcp](https://github.com/AGI_Ananas/bilibili-mcp)**（MIT）— B站 API Python MCP，在 `vendor/bilibili-mcp/`。

## 鸣谢

### 派生（代码直接继承并修改）
| 项目 | 源地址 | 许可 | 用途 |
|---|---|---|---|
| **dsh-bili-scroll** | https://github.com/c1overzz/dsh-bili-scroll | MIT | 本插件 fork 自其 v2.3.1 |
| **bilibili-mcp** | https://github.com/AGI_Ananas/bilibili-mcp | MIT | 内置于 `vendor/`，提供 B站 API |

### 借鉴（思想/逻辑参考，非直接拷代码）
| 项目 | 源地址 | 借鉴内容 |
|---|---|---|
| **dsh-bili-statistics** | https://github.com/apiaoapiao/dsh-bili-statistics | 观看历史 API 用法、统计维度（完成度、时间分布） |
| **dsh-whale-panel** | https://github.com/bilihave/dsh-whale-panel | 兴趣学习 + 半衰期淡忘、评论三层安全闸门 |
| **dsh-bili-eyes** | https://github.com/QiuYeDx/dsh-bili-eyes | 分镜多格拼图、收件箱自动分类 |
| **dsh-fish-memory** | https://github.com/Yuuyuko-uu/dsh-fish-memory | 半衰期打分公式思路（已用于 @dsh-cbw/memory） |

### 配套（运行时依赖，自研）
| 项目 | 源地址 | 用途 |
|---|---|---|
| **@dsh-cbw/memory** | https://github.com/Scorpioflz/dsh-cbw-memory | 爱好画像和兴趣词作为长期事实存储 |
| **dsh-web-service** | https://github.com/toddpan/dsh-webapi | 固定会话 REST/SSE API |

> 完整第三方声明：见 [`THIRD_PARTY_NOTICES.md`](./THIRD_PARTY_NOTICES.md)。

## 与上游的差异

| 方面 | 上游（dsh-bili-scroll） | 本 fork（@dsh-cbw/bilibili） |
|---|---|---|
| 值班会话 | 每轮一次性 CLI spawn，无上下文 | 固定 `sessionId` 复用（经 web-service），完整上下文（不可用时回退 CLI） |
| 主人识别 | `mentionName`（昵称，单个）+ `dmUid` | `masterUid`（UID）+ `mentionNames[]`（昵称）+ `dmRecipients[]` |
| 爱好分析 | 无 | `bili_user_favorites` / `bili_user_profile` / `bili_analyze_hobby` |
| 搜索关键词 | 死配置 `keywords[]` | 自动学习兴趣词 + 半衰期淡忘 |
| 帧图 | 单帧 `bili_frames` | 分镜多格拼图 |
| 评论闸门 | 语感闸门 + autoComment 闸门 | + 三层硬禁区（政治/政府/前沿） |
| 报告 | full/brief/off | + 统计维度（完成度、时间分布） |

## 安装

```bash
# 推荐（npm）
dsh plugin --profile web add @dsh-cbw/bilibili

# 从 GitHub
dsh plugin --profile web add github:Scorpioflz/dsh-cbw-bilibili

# 从本地路径
dsh plugin --profile web add /path/to/dsh-cbw-bilibili
```

> **前提**：机器上有 Python 3.10+ 或 `uv`。插件会自动下载 `uv`（若都没有）。
> **安装后**：重启 DSH（bundle 层需重启），进设置 → 刷B站 → 装依赖 → 扫码登录。
> **推荐配套**：安装 `dsh-web-service`（固定会话）和 `@dsh-cbw/memory`（画像持久化）。

## 配置

所有设置可通过 DSH 设置页或 `~/.dsh/bili-duty/config.json` 配置。

### 身份识别（本 fork 新增）
| 参数 | 默认 | 说明 |
|---|---|---|
| `masterUid` | `0` | 主人 B站 UID。用于爱好分析目标、私信监听白名单 |
| `mentionNames` | `[]` | 评论 @ 清单（昵称，可多个）。朋友也可加入 |
| `dmRecipients` | `[]` | 私信收件人清单（`{uid, name}`，可多个） |

### 固定会话（本 fork 新增）
| 参数 | 默认 | 说明 |
|---|---|---|
| `fixedSession` | `true` | 开启固定会话复用；web-service 不可用时自动回退 CLI |
| `sessionId` | `'bili-duty-fixed'` | 固定会话 ID |
| `webapiBase` | `''` | dsh-web-service 地址（空 = 自动探测 `127.0.0.1:3080`） |
| `webapiKey` | `''` | API Key（空 = 本机免鉴权） |

### 爱好分析（本 fork 新增）
| 参数 | 默认 | 说明 |
|---|---|---|
| `hobbyRefreshDays` | `30` | 主人画像刷新周期（天）。token 紧张可调 60/90/180 |
| `hobbyMaxVideos` | `500` | 单次分析最多遍历多少条收藏视频（控 token） |

### 继承自上游
| 参数 | 默认 | 说明 |
|---|---|---|
| `peakValley` | `false` | 峰谷自动调度 |
| `autoComment` | `false` | 自动评论 @ 用户 |
| `dmMode` | `'only'` | 私信投递：off / only / both |
| `keywords` | `[]` | 搜索关键词（空 = 用章程默认；兴趣学习开启后会被覆盖） |
| `workspace` | `''` | 值班轮工作目录（会话归该工作区） |
| `headlessProfile` | `'bili-duty'` | 值班轮 profile |
| `runTimeoutMinutes` | `20` | 每轮最多跑多久（分钟） |
| `modelProvider` | `''` | 值班轮模型 provider（空 = 跟随当前会话） |
| `modelName` | `''` | 值班轮模型名 |
| `reportLevel` | `'full'` | 日报形态：full / brief / off |
| `favFolderId` | `0` | 自动收藏目标夹 fid（0 = 不收藏） |

> 完整配置：见设置页或上游 [AGENTS.md](./AGENTS.md)。

## 使用

### 宿主工具（DSH 对话中直接用）
| 工具 | 说明 |
|---|---|
| `bili_whoami` | 看登录的是谁 |
| `bili_search` | 搜 B站视频 |
| `bili_subtitle` | 取视频 AI 字幕全文 |
| `bili_comments` | 取热门评论 |
| `bili_cover` / `bili_frames` | 看封面 / 帧 |
| `bili_reply` | 发评论/回复（有闸门） |
| `bili_status` / `bili_run` | 看值班状态 / 立刻跑一轮 |
| `bili_login_start` / `bili_login_poll` | 扫码登录 |
| `bili_setup` / `bili_config` | 建 venv / 读改设置 |
| `bili_user_favorites` | 列用户公开收藏夹 + 视频 *(新增)* |
| `bili_user_profile` | 获取用户公开信息 + 关注列表 *(新增)* |
| `bili_analyze_hobby` | 综合分析爱好 → 输出画像 JSON → 存入记忆 *(新增)* |

## 许可

MIT — 见 [LICENSE](./LICENSE)。含 `c1overzz/dsh-bili-scroll`（MIT）和 `bilibili-mcp`（MIT）的代码。

## 第三方声明

见 [THIRD_PARTY_NOTICES.md](./THIRD_PARTY_NOTICES.md)。