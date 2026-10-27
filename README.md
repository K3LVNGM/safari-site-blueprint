# Safari Site Blueprint

Prompts and Claude Code skills for rebuilding the **Asili Trails** setup on another business's WordPress site:

1. **A premium safari / tour operator website**, modelled on [asilitrails.com](https://asilitrails.com)
2. **A private operations portal** (lead board, tasks, calendar, insights, payments, proposals and more) in Apple Human Interface Guidelines style, modelled on the Asili Portal

## What's inside

| Path | What it is |
|---|---|
| [`prompts/01-safari-website.md`](prompts/01-safari-website.md) | Full build prompt for the public website: design system, global chrome, sitemap and page templates, content, SEO, working rules |
| [`prompts/02-operations-portal.md`](prompts/02-operations-portal.md) | Full build prompt for the operations portal: architecture, roles, HIG design tokens, screens, optional modules, PWA and push |
| [`skills/safari-website/`](skills/safari-website/SKILL.md) | Claude Code skill that runs the website prompt step by step |
| [`skills/operator-portal/`](skills/operator-portal/SKILL.md) | Claude Code skill that runs the portal prompt step by step |

## Option A: paste a prompt

1. Open the prompt file and replace every `{{…}}` placeholder with your business details.
2. Start a Claude Code session that can reach your WordPress site (a WordPress MCP server such as Novamira or the Elementor MCP, WP-CLI, or SSH).
3. Paste everything below the `---` line.

## Option B: install the skills

```bash
git clone https://github.com/GeorgeMollel/safari-site-blueprint.git
```

```bash
mkdir -p ~/.claude/skills && cp -R safari-site-blueprint/skills/* ~/.claude/skills/
```

Then ask Claude Code something like *"build a safari website for Kilele Tours like Asili Trails"* or *"add an operator portal to this site"*. Each skill carries its own copy of the spec (`reference.md`).

## Requirements

- WordPress 6.5+ with the **Hello Elementor** theme and **Elementor v4** (free; atomic widgets, no Pro needed)
- An SMTP plugin for email, since PHP `mail()` is unreliable on shared hosting
- Optional: an SEO plugin (Rank Math) for canonical, robots and sitemap

## Ground rules baked into the prompts

- Copy **patterns**, never other companies' text, reviews, prices, licences or photos.
- No made-up trust badges or awards.
- WCAG AA contrast, keyboard access, reduced-motion support, and testing at 375px wide.
- Back up before every change, and keep secrets out of code.

## License

MIT
