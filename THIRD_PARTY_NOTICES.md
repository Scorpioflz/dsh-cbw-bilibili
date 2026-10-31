# 第三方代码与署名说明

本插件（`@dsh-cbw/bilibili`）本体以 **MIT** 协议发布，正文见仓库根目录的 `LICENSE`。

`LICENSE` 包含两行版权声明：
- `Copyright (c) 2026 c1overzz` — 上游 `dsh-bili-scroll` 原始版权
- `Copyright (c) 2026 Scorpioflz` — 本 fork 的改动版权

MIT 允许改、再发、商用，二次分发时保留这两行和 MIT 正文即可。

## 一、直接派生（代码继承并修改）

### dsh-bili-scroll
- **源地址**：https://github.com/c1overzz/dsh-bili-scroll
- **派生版本**：v2.3.1（commit `a093f55`）
- **许可证**：MIT
- **用途**：本插件直接 fork 自它，继承 `index.js`、`client.js`、`lib/`、`prompts/`、`vendor/`、`profile-template/` 全部代码，在其上叠加改动
- **上游同步**：`git remote` 的 `upstream` 指向上游，可 `git fetch upstream && git merge upstream/main` 同步更新

## 二、内置第三方代码

### bilibili-mcp
- **目录**：`vendor/bilibili-mcp/`
- **源地址**：https://github.com/AGI_Ananas/bilibili-mcp
- **许可证**：MIT，Copyright (c) 2026 AGI_Ananas
- **用途**：B站 API 的 Python MCP 实现，值班轮通过它调用 B站搜索/字幕/评论/弹幕等
- **完整许可**：见该目录下的 `LICENSE`

## 三、借鉴（思想/逻辑参考，未直接拷代码）

### dsh-bili-statistics
- **源地址**：https://github.com/apiaoapiao/dsh-bili-statistics
- **借鉴内容**：观看历史 API（`api.bilibili.com/x/v2/history`）的调用方式；统计维度（完成度、时间分布、22 点后比例）的设计思路
- **用途**：用于本 fork 的观看统计报告功能
- **许可**：参考其仓库 LICENSE

### dsh-whale-panel
- **源地址**：https://github.com/bilihave/dsh-whale-panel
- **借鉴内容**：兴趣学习机制（按概率学习兴趣）+ 半衰期淡忘（兴趣随时间衰减）；评论三层硬禁区（政治/政府/前沿科学）
- **用途**：用于本 fork 的兴趣学习（替代死配置 keywords）和评论安全闸门
- **许可**：参考其仓库 LICENSE

### dsh-bili-eyes
- **源地址**：https://github.com/QiuYeDx/dsh-bili-eyes
- **借鉴内容**：分镜图多格拼接逻辑（每张 6~16 格，按剪辑节奏分格，约 384 token/张）；收件箱自动分类处理（回复/@/私信）
- **用途**：用于本 fork 的分镜图（替代单帧 bili_frames）和收件箱自动分类
- **许可**：参考其仓库 LICENSE

### dsh-fish-memory
- **源地址**：https://github.com/Yuuyuko-uu/dsh-fish-memory
- **借鉴内容**：半衰期打分公式思路（`A = A₀ × 2^(-days / H)`）
- **用途**：该思路已用于 @dsh-cbw/memory，本 fork 的兴趣学习复用同一机制
- **许可**：参考其仓库 LICENSE

## 四、运行时依赖（配套插件）

### @dsh-cbw/memory（自研）
- **源地址**：https://github.com/Scorpioflz/dsh-cbw-memory
- **用途**：爱好画像和兴趣词作为长期事实存储；值班轮搜索时从记忆取出画像生成关键词
- **关系**：同一作者（Scorpioflz）的自研插件，非第三方

### dsh-web-service
- **源地址**：https://github.com/toddpan/dsh-webapi
- **用途**：固定会话延续使用其 REST/SSE 会话 API（`POST /api/v1/sessions` + `POST /api/v1/sessions/{id}/prompt-stream`）
- **回退**：不可用时自动回退到一次性 CLI spawn（上游行为）

---

*本文件只是说明，不构成额外限制。所有代码均遵循其各自许可证。*