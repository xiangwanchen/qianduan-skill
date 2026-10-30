# design-studio

[English](README.en.md) | 简体中文

![skills](https://img.shields.io/badge/技能-11%20个（5%20核心%20+%206%20可选）-blue)
![size](https://img.shields.io/badge/包体-299%20文件%20%C2%B7%2028%20MB-lightgrey)
![license](https://img.shields.io/badge/许可-混合（MIT%2FApache%20×4%2C%20NC%20×1）-orange)
![agents](https://img.shields.io/badge/支持-WorkBuddy%20%C2%B7%20Claude%20Code%20%C2%B7%20Codex-green)

一个给编程 Agent 用的前端设计技能包。它把「多页架构 → 视觉方向 → 动效 → 交付前验收」串成一条流水线，
并把 5 个专职技能藏在**一个路由入口**后面 —— 让 Agent 只看见一个技能，而不是五个技能互相抢触发。

---

## 为什么要做成集合包

这些技能单独放着会严重打架：`ui-styling` 教 Tailwind + shadcn，`premium-website` 给的却是共享 CSS 架构；
`design`、`brand`、`design-system` 三个都会去抢同一句「帮我做套设计系统」。平铺在一起，普通建站任务会被它们劫持。

路由入口解决了这件事：它只预先加载一段很短的说明，判断清楚任务到底是什么类型，然后才打开对应的那个专职技能，
其余的全程不进上下文。

## 包里有什么

| 技能 | 作用 | 上游来源 | 许可 | 商用 |
|---|---|---|---|---|
| [`premium-website`](skills/premium-website/) | 多页站：共享 CSS/JS 架构、编辑级排版、anti-slop 规则 | [Sidiora-Labs/ion-agent-harness](https://github.com/Sidiora-Labs/ion-agent-harness) | MIT | ✅ |
| [`design-taste-frontend`](skills/design-taste-frontend/) | 从 247 个精选画廊站提炼的视觉方向；在真实浏览器里渲染并自检 | [Leonxlnx/taste-skill](https://github.com/Leonxlnx/taste-skill) v2.1.5 | MIT | ✅ |
| [`motion-ref`](skills/motion-ref/) | 13 大类共 **119** 个动效，按产品场景挑 | [joepUI/motion-ref-skill](https://github.com/joepUI/motion-ref-skill) | MIT | ✅ |
| [`impeccable`](skills/impeccable/) | 24 个命令、确定性反模式检测器、浏览器内迭代 | [pbakaus/impeccable](https://github.com/pbakaus/impeccable) v4.8.0 | Apache-2.0 | ✅ |
| [`ui-ux-pro-max`](skills/ui-ux-pro-max/) | 88 种风格、192 套配色、74 组字体配对、1934 个 Google 字体，可查询的 CSV | ui-ux-pro-max-skill | **CC-BY-NC-4.0** | ❌ |
| [`optional/`](optional/) ×6 | banner-design、brand、design、design-system、slides、ui-styling | 同上 | **CC-BY-NC-4.0** | ❌ |

## 环境依赖

| 工具 | 用途 |
|---|---|
| Node 22+ | taste skill 的 `draw.mjs` 与 `inspect.mjs` |
| Python 3.x | ui-ux-pro-max 的搜索引擎（只用标准库，无需 `pip install`） |
| Chrome / Edge / Chromium | taste skill 通过 CDP 在真实浏览器里渲染页面 |

impeccable 的引擎是自包含二进制，不需要运行时、不需要 API key、不联网。

## 安装

```bash
bash install.sh                 # 自动识别 WorkBuddy / Claude Code / Codex / Cursor
bash install.sh --target ~/.workbuddy/skills
bash install.sh --with-optional  # 同时把 6 个可选技能提升到顶层
```

**Windows 没有 bash 时**：把整个 `design-studio` 文件夹复制到你所用 Agent 的 skills 目录。

| Agent | skills 目录 |
|---|---|
| WorkBuddy | `%USERPROFILE%\.workbuddy\skills\` |
| Claude Code | `%USERPROFILE%\.claude\skills\` |
| Codex | `%USERPROFILE%\.codex\skills\` |

然后**重启会话** —— 技能在会话启动时加载，不会热更新。

## 它怎么工作

包根目录的 `SKILL.md` 就是路由。Agent 只加载它的名称和描述，别的什么都不会预先读进来。

```
1. 数据        ui-ux-pro-max        → 候选 token、配色、字体配对
2. 架构        premium-website      → 多页共享 CSS/JS（单页跳过此步）
3. 方向        design-taste-frontend→ 出三个方向，选一个，不取中间值
4. 动效        motion-ref           → 按场景匹配动效
5. 验收        impeccable           → detect + taste skill 的 inspect.mjs，在 1440/768/390 三个宽度渲染
```

路由还会先按任务类型分流，再决定交给谁：

| 如果用户想要… | 路由交给 |
|---|---|
| 一个多页站点 | premium-website → taste → motion-ref → impeccable |
| 一个单页落地页 | taste → motion-ref → impeccable |
| 只给现有界面加动效 | 只用 motion-ref |
| 只想做审查或审计 | 只用 impeccable |
| 只要配色 / 字体 / token | 只用 ui-ux-pro-max |

## 目录结构

```
design-studio/
├── SKILL.md              ← Agent 唯一能看见的入口
├── README.md             ← 中文
├── README.en.md          ← English
├── LICENSE-NOTES.md      ← 上游来源与商用红线
├── install.sh
├── skills/               ← 5 个核心技能
│   ├── premium-website/
│   ├── design-taste-frontend/
│   ├── motion-ref/
│   ├── impeccable/
│   └── ui-ux-pro-max/
├── optional/             ← 6 个窄用途技能，刻意冷藏
│   ├── banner-design/  brand/  design/
│   ├── design-system/  slides/  ui-styling/
└── agents/               ← impeccable 的 4 个 subagent
```

### 为什么 optional/ 被冷藏

这 6 个技能的描述词写得太宽，放在顶层会劫持普通建站任务，所以路由默认不点名它们中的任何一个。
只有在用户明确要那类产物时（banner、幻灯片、shadcn 组件集）才加载一个，而且要说明加载的是哪一个。

想永久启用：

```bash
bash install.sh --with-optional
```

## ⚠️ 商用红线

**`ui-ux-pro-max` 和 `optional/` 下所有内容都是 CC-BY-NC-4.0。** 个人学习、内部使用没问题，
但拿去产出**付费客户的交付物**，需要作者授权。

接商业项目时，删掉这两条路径即可：

```bash
rm -rf skills/ui-ux-pro-max optional
```

剩下 4 个技能是 MIT 或 Apache-2.0，能完整覆盖同样的工作 —— 路由会优雅降级，只是跳过数据步骤，改为向你索取 token。

以上不构成法律意见。给客户开票之前，请自行核对 `LICENSE-NOTES.md` 和各上游许可。

## Windows 路径坑

在 Git Bash 里，把 `~/...` 这样的路径交给 `python.exe` 或 `node.exe`，会被解析成 `C:\c\Users\...` 而报文件不存在。
本包内所有脚本路径都写成了绝对的 `C:/Users/<你>/...`，路由也要求模型照此办理。

## 来源

2026-10-31 由上文致谢的各个上游项目组装而成。版本冻结在当时的状态 —— 各上游独立发版，
要更新请单独更新，别指望这个包会跟着同步。

本 README 里的数字都是对包内文件实测数出来的，不是照抄上游 README
（正因如此，motion-ref 这里写 119 个动效，而不是上游说的 112）。

## 许可

按技能各自不同，见上表和 `LICENSE-NOTES.md`。每个技能自己的许可文件与署名都随它放在各自的文件夹内。
