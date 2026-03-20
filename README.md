<div align="center">

# claude-content-engine

**A content creation engine for Claude Code.**

8 specialized skills. Persistent memory. A built-in quality gate that kills AI slop.<br>
Install in one command. Zero config.

[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Skills](https://img.shields.io/badge/skills-8-green.svg)](#skills)
[![Claude Code](https://img.shields.io/badge/Claude_Code-plugin-8A2BE2.svg)](https://claude.ai/code)

[Install](#install) · [Skills](#skills) · [Advanced Features](#advanced-features) · [Contributing](CONTRIBUTING.md)

</div>

---

## Why this exists

Claude is already a strong writer. But there's a difference between "write me a blog post" and giving it a full content playbook - voice profiles, SEO structure, copywriting frameworks, platform-specific formatting, and a quality gate that catches lazy patterns before you see them.

**claude-content-engine** installs 8 content skills, a persistent memory system, and an automatic slop detector into Claude Code. One command, zero config.

You just ask naturally:

```
> Turn this blog post into a Twitter thread, LinkedIn post, and newsletter snippet
```

Claude picks the right skill, loads your saved voice profile, writes all three pieces, and the quality gate checks the output automatically before you see it.

## Install

```bash
curl -fsSL https://raw.githubusercontent.com/quionie/claude-content-engine/main/install.sh | bash
```

Requires `git`, `jq`, and `python3` (for the quality gate hooks).

Restart Claude Code. Done.

<details>
<summary>Manual install / update / uninstall</summary>

**Clone manually:**
```bash
git clone https://github.com/quionie/claude-content-engine.git \
  ~/.claude/plugins/marketplaces/claude-content-engine
```

**Update:**
```bash
~/.claude/plugins/marketplaces/claude-content-engine/install.sh --update
```

**Uninstall:**
```bash
~/.claude/plugins/marketplaces/claude-content-engine/install.sh --uninstall
```

</details>

## Skills

### Core Skills

| Skill | What it does | Example prompt |
|:------|:-------------|:---------------|
| **Content Repurposer** | Transforms one piece of content into multiple platform-native formats | *"Repurpose this blog post for Twitter, LinkedIn, and my newsletter"* |
| **Blog Post Architect** | SEO-optimized blog posts with structure, meta descriptions, and internal linking strategy | *"Write a blog post about async communication for remote teams"* |
| **Copywriting Engine** | Headlines, taglines, landing pages, ad copy - using PAS, AIDA, and BAB frameworks | *"Write landing page copy for a project management tool"* |
| **Brand Voice Builder** | Analyzes writing samples and outputs a reusable voice profile document | *"Analyze these 5 blog posts and capture my writing voice"* |
| **Email Sequence Builder** | Full email drip campaigns - welcome, launch, onboarding, re-engagement, abandoned cart | *"Build a 7-email welcome sequence for my SaaS"* |
| **Social Media Calendar** | Generates a week or month of scheduled social content with platform-specific formatting | *"Create 2 weeks of social media content for my startup"* |

### Advanced Skills

| Skill | What it does | Example prompt |
|:------|:-------------|:---------------|
| **Content Workflow** | Runs multiple skills in sequence - output from one feeds into the next | *"Analyze my voice, write a blog post in it, then repurpose for social"* |
| **Content Memory** | Stores your voice, audience, pillars, and preferences across sessions | *"Remember my brand voice for future sessions"* |

## Advanced Features

Skills, memory, and hooks working together.

### 1. Skill Chaining

You can chain skills together. The output from one step gets used as input for the next.

```
Step 1: Brand Voice Builder   → extracts your voice from samples
            ↓ voice profile
Step 2: Blog Post Architect   → writes a post in your voice
            ↓ blog post
Step 3: Content Repurposer    → creates Twitter thread + LinkedIn + newsletter
            ↓ 3 content pieces
Step 4: Social Media Calendar  → schedules everything into a 2-week plan
```

**5 pre-built workflows included:** Content Machine, Brand Launch Kit, Blog-to-Everywhere, Voice-First Content Sprint, and Email Empire. Or just describe what you want and it'll pick the right skills to run in sequence.

### 2. Content Memory

Your content context persists across sessions. No more re-explaining your brand every time.

```
~/.claude-content-engine/memory/
├── voice-profile.md     ← saved automatically after Brand Voice Builder runs
├── content-pillars.md   ← your recurring themes and topics
├── audience.md          ← who you're writing for
├── style-prefs.md       ← "don't use emojis", "always be casual", etc.
├── top-content.md       ← log of your best-performing posts
└── context.md           ← product, positioning, competitors
```

Memory is **local-only** (stored on your machine, never uploaded), **opt-in** (you control what's saved), and **auto-loaded** (skills reference it automatically so you don't repeat yourself).

### 3. Quality Gate (AI Slop Detector)

A hook that runs automatically on every piece of content Claude writes. It catches:

- **Hard slop** - "delve", "tapestry", "in today's fast-paced world", "it's important to note", "game-changer", "seamlessly" - and 30+ more patterns
- **Soft slop** - "let's dive in", "as we've seen", "the landscape of" - flagged when multiple appear together
- **Weak copy** - "very good", "in order to", "I think that" - with specific rewrite suggestions
- **Fake enthusiasm** - excessive exclamation marks that read as performative

Claude gets the feedback and rewrites before you see the final output. You don't have to do anything.

```
PostToolUse hook → scans written content → flags issues → Claude rewrites
Stop hook → final pass → catches anything that slipped through
```

## How It Works

Skills are `.md` files with YAML frontmatter. Claude reads the `description` field and auto-activates the right skill based on your prompt. No slash commands needed.

```yaml
---
name: skill-name
description: When to activate this skill (trigger conditions)
version: 1.0.0
---

# Skill Name
[Detailed instructions for Claude]
```

The hooks are Python scripts that run via Claude Code's [hook system](https://docs.anthropic.com/en/docs/claude-code/hooks). They inspect content post-write and inject feedback before Claude finalizes its response.

## Architecture

```
claude-content-engine/
├── skills/                        # 8 content skills
│   ├── content-repurposer/SKILL.md
│   ├── blog-post-architect/SKILL.md
│   ├── copywriting-engine/SKILL.md
│   ├── brand-voice-builder/SKILL.md
│   ├── email-sequence-builder/SKILL.md
│   ├── social-media-calendar/SKILL.md
│   ├── content-workflow/SKILL.md       ← skill chaining orchestrator
│   └── content-memory/SKILL.md         ← persistent content memory
├── hooks/                         # quality gate system
│   ├── hooks.json                      ← hook configuration
│   ├── quality_gate.py                 ← PostToolUse AI slop detector
│   └── content_review.py              ← Stop hook final pass
├── .claude-plugin/plugin.json     # plugin metadata
├── install.sh                     # one-line installer
├── CONTRIBUTING.md
└── LICENSE
```

## FAQ

<details>
<summary><strong>Do I need a specific Claude plan?</strong></summary>
Skills work with any Claude Code subscription that supports custom skills and hooks.
</details>

<details>
<summary><strong>Can I use only some skills?</strong></summary>
Yes. Delete any <code>skills/&lt;name&gt;/</code> directory you don't want. The remaining skills work independently.
</details>

<details>
<summary><strong>Will these conflict with my existing skills?</strong></summary>
Shouldn't. Claude picks the most relevant skill based on your prompt. If you have overlapping skills, you can always delete the one you don't want.
</details>

<details>
<summary><strong>Can I disable the quality gate?</strong></summary>
Yes. Delete <code>hooks/</code> or remove entries from <code>hooks/hooks.json</code>. The skills work fine without it.
</details>

<details>
<summary><strong>Where is memory stored?</strong></summary>
Locally at <code>~/.claude-content-engine/memory/</code>. Nothing is uploaded. Run <code>rm -rf ~/.claude-content-engine</code> to wipe it.
</details>

<details>
<summary><strong>Can I modify skills?</strong></summary>
Yes. They're markdown files. Edit them, fork them, rewrite them entirely.
</details>

## Contributing

We accept new skills. See [CONTRIBUTING.md](CONTRIBUTING.md) for the format and quality checklist.

## License

[MIT](LICENSE)
