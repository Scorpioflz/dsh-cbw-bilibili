# @dsh-cbw/bilibili

<p align="center">
  <a href="./README_ZH.md">中文</a> | <a href="./README.md">English</a>
</p>

A DeepSeek Harness (DSH) plugin that watches Bilibili for you — searches, reads subtitles, picks worth-your-time videos, writes a daily report, and @-mentions you. Forked from [`dsh-bili-scroll`](https://github.com/c1overzz/dsh-bili-scroll) v2.3.1 with significant enhancements: fixed-session continuity, master profile analysis, hobby画像, interest learning with half-life decay, storyboard frames, and comment safety gates.

## Features

### Inherited from upstream (dsh-bili-scroll v2.3.1)
- **Duty rounds** — peak/valley scheduling, searches Bilibili, reads subtitles, writes daily report
- **13 host tools** — `bili_whoami`, `bili_search`, `bili_subtitle`, `bili_comments`, `bili_cover`, `bili_frames`, `bili_reply`, `bili_status`, `bili_run`, `bili_login_start/poll`, `bili_setup`, `bili_config`
- **MCP duty-round tools** — search/recommend, subtitle/comments/danmaku, cover/frames, reply/send_message, transcribe
- **DM watch** — listens for master's private messages, replies with session continuity
- **Auto-favorite** — host-side `favops.py`
- **Configurable model** — `modelProvider`/`modelName` per duty round
- **Timeout watchdog** — `runTimeoutMinutes` (default 20)

### Added in this fork
- **Fixed session continuity** — duty rounds reuse a fixed `sessionId` via CLI `--session-id` parameter, preserving full conversation context across rounds. Fixed session skips duty bucket archiving.
- **Master profile & hobby analysis** — `masterUid` identifies the master; `bili_analyze_hobby` analyzes public favorites + following list → hobby画像 → injected into @dsh-cbw/memory as long-term facts. Configurable refresh cycle (`hobbyRefreshDays`, default 30).
- **Decoupled identity** — `masterUid` (master, by UID) vs `mentionNames` (comment @-list, by nickname, multiple) vs `dmRecipients` (DM recipients, multiple). Friends can be added to share recommendations without being the master.
- **Interest learning with half-life decay** — replaces static `keywords` config; auto-extracts interest words from picked videos, decays over time. *[Borrowed from bilihave/dsh-whale-panel]*
- **Storyboard frames** — multi-grid拼接 instead of single frames, higher token efficiency. *[Borrowed from QiuYeDx/dsh-bili-eyes]*
- **Comment three-layer safety gate** — hard blocks for politics/government/frontier-science. *[Borrowed from bilihave/dsh-whale-panel]*
- **Watch statistics report** — completion rate (watched/skipped/partial), hourly time distribution, late-night ratio (22:00–2:00), top UPs in daily report. *[Borrowed from apiaoapiao/dsh-bili-statistics]*
- **Inbox auto-classification** — replies/@/likes/DM categorized and listed in daily report (read-only, no auto-reply). *[Borrowed from QiuYeDx/dsh-bili-eyes]*

## Derived From

This plugin is **directly forked** from [`c1overzz/dsh-bili-scroll`](https://github.com/c1overzz/dsh-bili-scroll) v2.3.1 (MIT License). The upstream `upstream` git remote is preserved for syncing future upstream updates:

```bash
git fetch upstream
git merge upstream/main
```

All upstream code (`index.js`, `client.js`, `lib/`, `prompts/`, `vendor/`, `profile-template/`) is inherited; modifications are layered on top.

## Dependencies

### Required (for added features)
- **[dsh-web-service](https://github.com/toddpan/dsh-webapi)** — optional; fixed-session uses CLI `--session-id` (not web-service REST). web-service base URL configurable via `webapiBase` for P1 hobby analysis tool API.
- **[@dsh-cbw/memory](https://github.com/Scorpioflz/dsh-cbw-memory)** — hobby画像 and interest words are stored as long-term facts. Optional but recommended; without it,画像 is only kept locally in `hobby.json`.

### Bundled
- **[bilibili-mcp](https://github.com/AGI_Ananas/bilibili-mcp)** (MIT) — Bilibili API Python MCP, in `vendor/bilibili-mcp/`.

## Acknowledgements

### Derived (code directly inherited & modified)
| Project | Source | License | Usage |
|---|---|---|---|
| **dsh-bili-scroll** | https://github.com/c1overzz/dsh-bili-scroll | MIT | This plugin is forked from it v2.3.1 |
| **bilibili-mcp** | https://github.com/AGI_Ananas/bilibili-mcp | MIT | Bundled in `vendor/`, provides Bilibili API |

### Borrowed (ideas/logic referenced, not directly copied)
| Project | Source | Borrowed concept |
|---|---|---|
| **dsh-bili-statistics** | https://github.com/apiaoapiao/dsh-bili-statistics | Watch history API usage, statistics dimensions (completion, time distribution) |
| **dsh-whale-panel** | https://github.com/bilihave/dsh-whale-panel | Interest learning + half-life decay, comment three-layer safety gate |
| **dsh-bili-eyes** | https://github.com/QiuYeDx/dsh-bili-eyes | Storyboard multi-grid frames, inbox auto-classification |
| **dsh-fish-memory** | https://github.com/Yuuyuko-uu/dsh-fish-memory | Half-life scoring formula concept (already used in @dsh-cbw/memory) |

### Companion (runtime dependencies, self-developed)
| Project | Source | Usage |
|---|---|---|
| **@dsh-cbw/memory** | https://github.com/Scorpioflz/dsh-cbw-memory | Stores hobby画像 and interest words as long-term facts |
| **dsh-web-service** | https://github.com/toddpan/dsh-webapi | Fixed-session REST/SSE API |

> Full third-party notices: see [`THIRD_PARTY_NOTICES.md`](./THIRD_PARTY_NOTICES.md).

## Differences from Upstream

| Area | Upstream (dsh-bili-scroll) | This fork (@dsh-cbw/bilibili) |
|---|---|---|
| Duty session | One-shot CLI spawn per round, no context | Fixed `sessionId` reuse via CLI `--session-id`, full context; fixed session skips bucket archiving |
| Master identity | `mentionName` (nickname, single) + `dmUid` | `masterUid` (UID) + `mentionNames[]` (nicknames) + `dmRecipientUids[]` (UIDs) |
| Hobby analysis | None | `bili_user_favorites` / `bili_user_profile` / `bili_analyze_hobby` |
| Search keywords | Static `keywords[]` config | Auto-learned interest words with half-life decay |
| Frames | Single frame `bili_frames` | Storyboard multi-grid拼接 `bili_storyboard` |
| Comment gate | Tone gate + autoComment gate | + three-layer hard block (politics/government/frontier) |
| Report | Full/brief/off | + watch statistics (completion/time distribution/late-night ratio) + inbox classification (replies/@/likes) |

## Installation

```bash
# Recommended (npm)
dsh plugin --profile web add @dsh-cbw/bilibili

# From GitHub
dsh plugin --profile web add github:Scorpioflz/dsh-cbw-bilibili

# From local path
dsh plugin --profile web add /path/to/dsh-cbw-bilibili
```

> **Prerequisite**: Python 3.10+ or `uv` on the machine. The plugin auto-downloads `uv` if neither is present.
> **After install**: restart DSH (bundle layer needs restart), then go to Settings → 刷B站 → install dependencies → scan-login.
> **Recommended companions**: install `dsh-web-service` (for fixed-session) and `@dsh-cbw/memory` (for画像 persistence).

## Configuration

All settings are configurable via the DSH settings page or `~/.dsh/bili-duty/config.json`.

### Identity (new in this fork)
| Parameter | Default | Description |
|---|---|---|
| `masterUid` | `0` | Master's Bilibili UID. Used for hobby analysis target, DM watch whitelist |
| `mentionNames` | `[]` | Comment @-list (nicknames, multiple). Friends can be included |
| `dmRecipientUids` | `[]` | DM recipients UIDs (string array, multiple) |

### Fixed session (new in this fork)
| Parameter | Default | Description |
|---|---|---|
| `fixedSession` | `true` | Enable fixed-session reuse via CLI `--session-id` |
| `dutySessionId` | `'bili-duty-fixed'` | Fixed session ID for duty rounds |
| `dmSessionId` | `'bili-dm-fixed'` | Fixed session ID for DM replies |
| `webapiBase` | `''` | dsh-web-service base URL (for P1 hobby analysis API; empty = auto-detect `127.0.0.1:3080`) |
| `webapiKey` | `''` | API Key (empty = local no-auth) |

### Hobby analysis (new in this fork)
| Parameter | Default | Description |
|---|---|---|
| `hobbyRefreshDays` | `30` | Master画像 refresh cycle in days (increase to 60/90/180 if token-heavy) |
| `hobbyMaxVideos` | `500` | Max favorite videos to analyze per refresh (controls token cost) |

### Watch statistics & inbox (new in this fork)
| Parameter | Default | Description |
|---|---|---|
| `watchedStatsEnabled` | `true` | Append watch statistics section to daily report |
| `watchedStatsDays` | `7` | Lookback days for statistics (1–30) |
| `inboxScanEnabled` | `false` | Scan inbox (replies/@/likes) and classify in daily report |
| `inboxScanPerRound` | `5` | Max items per category per round (1–20) |

### Interest learning & comment gate (new in this fork)
| Parameter | Default | Description |
|---|---|---|
| `interestLearnEnabled` | `true` | Auto-extract interest words from picked videos with half-life decay |
| `interestHalfLifeDays` | `14` | Half-life for interest word scores (1–90 days) |
| `commentHardGate` | `true` | Three-layer hard block for comments (politics/government/frontier) |

### Inherited from upstream
| Parameter | Default | Description |
|---|---|---|
| `peakValley` | `false` | Peak/valley auto-scheduling |
| `autoComment` | `false` | Auto-comment and @ user |
| `dmMode` | `'only'` | DM delivery: off / only / both |
| `keywords` | `[]` | Search keywords (empty = use charter defaults; overridden by interest learning) |
| `workspace` | `''` | Duty round working directory (session归该工作区) |
| `headlessProfile` | `'bili-duty'` | Profile for duty rounds |
| `runTimeoutMinutes` | `20` | Max minutes per round |
| `modelProvider` | `''` | Model provider for duty rounds (empty = follow current session) |
| `modelName` | `''` | Model name for duty rounds |
| `reportLevel` | `'full'` | Report form: full / brief / off |
| `favFolderId` | `0` | Auto-favorite target folder fid (0 = off) |

> Full config list: see settings page or upstream [AGENTS.md](./AGENTS.md).

## Usage

### Host tools (use in DSH conversation)
| Tool | Description |
|---|---|
| `bili_whoami` | Check who is logged in |
| `bili_search` | Search Bilibili videos |
| `bili_subtitle` | Get video AI subtitle full text |
| `bili_comments` | Get hot comments |
| `bili_cover` / `bili_frames` | View cover / frames |
| `bili_reply` | Post comment/reply (gated) |
| `bili_status` / `bili_run` | Check duty status / run a round now |
| `bili_login_start` / `bili_login_poll` | Scan-code login |
| `bili_setup` / `bili_config` | Setup venv / read-edit config |
| `bili_user_favorites` | List user's public favorites + videos *(new)* |
| `bili_user_profile` | Get user's public info + following list *(new)* |
| `bili_analyze_hobby` | Analyze hobby → output画像 JSON → store to memory *(new)* |
| `bili_storyboard` | Multi-grid storyboard frames (6 frames at once) *(new)* |
| `bili_watched_stats` | Compute watch statistics (completion/time distribution/late-night ratio) *(new)* |
| `bili_inbox` | Scan & classify inbox (replies/@/likes/DM) *(new)* |

## License

MIT — see [LICENSE](./LICENSE). Contains code from `c1overzz/dsh-bili-scroll` (MIT) and `bilibili-mcp` (MIT).

## Third Party Notices

See [THIRD_PARTY_NOTICES.md](./THIRD_PARTY_NOTICES.md) for full third-party attributions.