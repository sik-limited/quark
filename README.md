<h1 align="center">Quark</h1>

<p align="center">
  <a href="https://skills.sh/sik-limited/quark"><img src="https://skills.sh/b/sik-limited/quark" alt="skills.sh"></a>
</p>

<p align="center"><strong>English</strong> · <a href="README.ko.md">한국어</a> · <a href="README.ja.md">日本語</a> · <a href="README.zh-TW.md">繁體中文</a></p>

<p align="center">Open-source <a href="https://agentskills.io/">Agent Skills</a> and <code>DESIGN.md</code> references for agentic design with Codex, Claude Code, and Cursor.</p>

<p align="center"><a href="https://design.sik.limited/"><strong>Quark website</strong></a> · Preview UI components and design tokens</p>

```sh
npx skills add sik-limited/quark
```

## Skills

| Skill | What it does |
| --- | --- |
| [`quark-ui`](skills/quark-ui/SKILL.md) | Build interfaces with Quark components and design tokens |
| [`design-to-code`](skills/design-to-code/SKILL.md) | Turn screenshots, Figma frames, and design specs into code |
| [`design-check`](skills/design-check/SKILL.md) | Review an existing interface and prioritize fixes |
| [`q-clean`](skills/q-clean/SKILL.md) | Refactor UI into components and fix state, motion, responsive layout, and accessibility issues |
| [`component-api`](skills/component-api/SKILL.md) | Design reusable component APIs, including props, states, and composition across frameworks |
| [`design-tokens`](skills/design-tokens/SKILL.md) | Define tokens for color, spacing, radius, shadows, motion, and themes |
| [`animate`](skills/animate/SKILL.md) | Build UI transitions, springs, and gestures with support for reduced motion |
| [`interface-polish`](skills/interface-polish/SKILL.md) | Refine corner radii, optical alignment, borders, and icons |
| [`typography`](skills/typography/SKILL.md) | Set type scales, line heights, wrapping, numerals, and font loading |
| [`layout`](skills/layout/SKILL.md) | Review grouping, alignment, spacing, responsive layouts, and right-to-left text |
| [`accessibility`](skills/accessibility/SKILL.md) | Check semantics, focus, keyboard controls, and touch targets against WCAG 2.2 AA |
| [`interface-copy`](skills/interface-copy/SKILL.md) | Write labels, errors, empty states, and confirmation prompts |
| [`stress-test`](skills/stress-test/SKILL.md) | Find where UI breaks with long text, empty data, narrow screens, and errors |
| [`interface-review`](skills/interface-review/SKILL.md) | Review a change and organize findings by impact |

## Install

With Node.js and npm installed, run the command for your agent from the project directory. The command at the top lets you choose both the skills and the agent. [Skills CLI documentation](https://www.skills.sh/docs/cli)

### Codex

```sh
npx skills add sik-limited/quark --agent codex
```

### Claude Code

```sh
npx skills add sik-limited/quark --agent claude-code
```

### Cursor

```sh
npx skills add sik-limited/quark --agent cursor
```

Add `--skill quark-ui` to install just that skill, `--skill '*'` to install the full collection, or `--global` to use the skills across projects. Install the full collection when using `q-clean` or `design-check`, since they draw on other Quark skills.

### Install from the Quark website

Download `SKILL.md` from the [Quark UI skill page](https://design.sik.limited/en/skills/quark-ui/), or pass the file URL directly to the Skills CLI.

```sh
npx skills add https://design.sik.limited/skills/quark-ui/SKILL.md
```

To install manually, copy the skill folder from `skills/` into your agent's skills directory. In Claude on the web, zip the skill folder and upload it. In ChatGPT on the web, you can upload a skill if your workspace supports it. [Claude help](https://support.claude.com/en/articles/12512180-use-skills-in-claude) · [ChatGPT help](https://help.openai.com/en/articles/20001066-skills-in-chatgpt)

## Try a skill

In your agent, point to the screen you want to review and try this prompt.

```text
Use design-check to review this settings screen for mobile layout, keyboard focus, and error states. List the issues you find and prioritize the fixes.
```

## DESIGN.md

Choose a reference, save it as `DESIGN.md` at the root of your project, and ask your agent to read it before making UI changes. Quark compiled these independent references from public design systems and product screens.

| Reference | What to look for | Links |
| --- | --- | --- |
| Claude | Warm colors, coral buttons, and editorial typography | [Document](designs/claude/DESIGN.md) · [Preview](https://design.sik.limited/en/themes/claude/) |
| KRDS | Public service interfaces, accessibility, forms, and navigation | [Document](designs/krds/DESIGN.md) · [Preview](https://design.sik.limited/en/themes/krds/) |
| Pinterest | Image-led discovery, masonry grids, and filters | [Document](designs/pinterest/DESIGN.md) · [Preview](https://design.sik.limited/en/themes/pinterest/) |
| RIDI | Content browsing, book cover cards, and ranked lists | [Document](designs/ridi/DESIGN.md) · [Preview](https://design.sik.limited/en/themes/ridi/) |
| SEED | Semantic colors, grids, and Korean UX writing | [Document](designs/seed/DESIGN.md) · [Preview](https://design.sik.limited/en/themes/seed/) |
| Toss | Finance UI, mobile touch controls, and button and input states | [Document](designs/toss/DESIGN.md) · [Preview](https://design.sik.limited/en/themes/toss/) |
| Wanted (Montage) | Responsive grids, typography, and shared component states | [Document](designs/wanted/DESIGN.md) · [Preview](https://design.sik.limited/en/themes/wanted/) |
| xAI | Dark UI, white buttons, thin borders, and flat panels | [Document](designs/xai/DESIGN.md) · [Preview](https://design.sik.limited/en/themes/xai/) |

## License

Quark's original skills and design references are released under the [MIT License](LICENSE). Brand names, trademarks, fonts, and third-party materials belong to their respective owners.
