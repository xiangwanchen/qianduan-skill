# design-studio

![skills](https://img.shields.io/badge/skills-11%20(5%20core%20%2B%206%20optional)-blue)
![size](https://img.shields.io/badge/pack-299%20files%20%C2%B7%2028%20MB-lightgrey)
![license](https://img.shields.io/badge/license-mixed%20(4%20MIT%2FApache%2C%201%20NC)-orange)
![agents](https://img.shields.io/badge/works%20with-WorkBuddy%20%C2%B7%20Claude%20Code%20%C2%B7%20Codex-green)

A frontend design pack for coding agents. It chains multi-page architecture, visual art direction,
motion and pre-ship verification into one pipeline, and hides five specialist skills behind a single
router — so your agent sees **one** entry point instead of five skills fighting over the same trigger.

---

## Why a pack

Individually these skills overlap badly. `ui-styling` teaches Tailwind + shadcn while
`premium-website` ships a shared-CSS architecture; `design`, `brand` and `design-system` all reach
for the same "make me a design system" prompt. Load them side by side and they hijack ordinary
site work.

The router fixes that. It loads one short file up front, decides what the job actually is, and then
opens exactly the specialist that fits. The rest stay out of your context.

## What's inside

| Skill | What it does | Upstream | License | Commercial |
|---|---|---|---|---|
| [`premium-website`](skills/premium-website/) | Multi-page sites: shared CSS/JS architecture, editorial typography, anti-slop rules | [Sidiora-Labs/ion-agent-harness](https://github.com/Sidiora-Labs/ion-agent-harness) | MIT | ✅ |
| [`design-taste-frontend`](skills/design-taste-frontend/) | Art direction distilled from 247 curated gallery sites; renders and self-checks in a real browser | [Leonxlnx/taste-skill](https://github.com/Leonxlnx/taste-skill) v2.1.5 | MIT | ✅ |
| [`motion-ref`](skills/motion-ref/) | 119 effects across 13 categories, picked by product scenario | [joepUI/motion-ref-skill](https://github.com/joepUI/motion-ref-skill) | MIT | ✅ |
| [`impeccable`](skills/impeccable/) | 24 commands, deterministic anti-pattern detection, browser iteration | [pbakaus/impeccable](https://github.com/pbakaus/impeccable) v4.8.0 | Apache-2.0 | ✅ |
| [`ui-ux-pro-max`](skills/ui-ux-pro-max/) | 88 styles, 192 palettes, 74 font pairings, 1,934 Google fonts as queryable CSV | ui-ux-pro-max-skill | **CC-BY-NC-4.0** | ❌ |
| [`optional/`](optional/) ×6 | banner-design, brand, design, design-system, slides, ui-styling | same as above | **CC-BY-NC-4.0** | ❌ |

## Requirements

| Tool | Needed for |
|---|---|
| Node 22+ | taste skill's `draw.mjs` and `inspect.mjs` |
| Python 3.x | ui-ux-pro-max's search engine (stdlib only, no `pip install`) |
| Chrome / Edge / Chromium | taste skill renders pages in a real browser via CDP |

Impeccable's engine is a self-contained binary — no runtime, no API key, no network.

## Install

```bash
bash install.sh                 # auto-detects WorkBuddy / Claude Code / Codex / Cursor
bash install.sh --target ~/.workbuddy/skills
bash install.sh --with-optional  # also promote the 6 optional skills to top level
```

**Windows without bash:** copy the whole `design-studio` folder into your agent's skills directory.

| Agent | Skills directory |
|---|---|
| WorkBuddy | `%USERPROFILE%\.workbuddy\skills\` |
| Claude Code | `%USERPROFILE%\.claude\skills\` |
| Codex | `%USERPROFILE%\.codex\skills\` |

Then **restart the session** — skills load at startup, not on the fly.

## How it works

`SKILL.md` at the pack root is the router. The agent loads its name and description, nothing else.

```
1. Data          ui-ux-pro-max        → candidate tokens, palettes, font pairings
2. Architecture  premium-website      → multi-page shared CSS/JS (skip for single page)
3. Direction     design-taste-frontend→ three directions, pick one, never average them
4. Motion        motion-ref           → effects matched to the scenario
5. Verify        impeccable           → detect + taste skill inspect.mjs at 1440/768/390
```

The router also picks by task type before it picks by skill:

| If the user wants… | Router sends it to |
|---|---|
| A multi-page site | premium-website → taste → motion-ref → impeccable |
| A single landing page | taste → motion-ref → impeccable |
| Just motion on existing UI | motion-ref only |
| Just a critique or audit | impeccable only |
| Just colors / fonts / tokens | ui-ux-pro-max only |

## Layout

```
design-studio/
├── SKILL.md              ← the only entry point the agent sees
├── README.md
├── LICENSE-NOTES.md      ← upstream sources and the commercial-use line
├── install.sh
├── skills/               ← 5 core specialists
│   ├── premium-website/
│   ├── design-taste-frontend/
│   ├── motion-ref/
│   ├── impeccable/
│   └── ui-ux-pro-max/
├── optional/             ← 6 narrow skills, deliberately parked
│   ├── banner-design/  brand/  design/
│   ├── design-system/  slides/  ui-styling/
└── agents/               ← impeccable's 4 subagents
```

### Why `optional/` is parked

Those six have broad descriptions. At top level they hijack ordinary site work, so the router names
none of them by default. Load one only when the user asks for that specific artifact — a banner, a
slide deck, a shadcn component set — and say which one you loaded.

To enable them permanently:

```bash
bash install.sh --with-optional
```

## ⚠️ Commercial use

**`ui-ux-pro-max` and everything in `optional/` are CC-BY-NC-4.0.** They are fine for personal,
learning and internal work. Using them to produce a **paid client deliverable** requires the
author's permission.

For commercial jobs, delete two paths:

```bash
rm -rf skills/ui-ux-pro-max optional
```

The remaining four skills are MIT or Apache-2.0 and cover the same ground — the router degrades
gracefully, it just skips the data step and asks you for tokens instead.

Not legal advice. Check `LICENSE-NOTES.md` and each upstream before you bill a client.

## Windows path note

In Git Bash, a `~/...` path handed to `python.exe` or `node.exe` resolves to `C:\c\Users\...` and
fails. Every script path in this pack is written as an absolute `C:/Users/<you>/...`, and the
router tells the model to do the same.

## Origin

Assembled 2026-10-31 from the upstream projects credited above. Versions are frozen at what was
current then — each upstream ships independently, so refresh them individually rather than
expecting the pack to track them.

Counts in this README were measured against the files in the pack, not copied from upstream
READMEs (that is why motion-ref says 119 effects, not 112).

## License

Mixed, per skill — see the table above and `LICENSE-NOTES.md`. Each skill's own license file and
attribution travel with it inside its folder.
