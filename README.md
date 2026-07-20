# accessible-semantic-html

A Claude Code skill that makes Claude default to accessible, semantic HTML, CSS,
and modern JavaScript — proper landmarks and elements, correct ARIA usage, keyboard
support, focus/motion/contrast-aware styling, and current (non-legacy) web platform
APIs — whenever it writes or reviews frontend code.

## What's included

```
accessible-semantic-html/
├── SKILL.md                       Core principles, workflow, checklist
├── references/
│   ├── aria-patterns.md           Dialog, tabs, menu, combobox, live regions
│   ├── accessible-css.md          Focus states, motion/contrast/color-scheme, layout
│   └── modern-apis.md             Current HTML/JS APIs vs. what they replace
└── evals/
    └── evals.json                 Test prompts used to validate the skill
```

## Install (Claude Code)

Claude Code loads skills from `.claude/skills/` — either per-project or globally
in your home directory.

**Per-project (recommended for a single repo):**

```bash
mkdir -p /path/to/your-project/.claude/skills
cp -r accessible-semantic-html /path/to/your-project/.claude/skills/
```

**Global (available in every project):**

```bash
mkdir -p ~/.claude/skills
cp -r accessible-semantic-html ~/.claude/skills/
```

No restart needed — Claude Code picks up skills from that directory automatically.
Claude will consult it whenever a task involves writing or reviewing HTML/CSS/JS,
without needing to be asked explicitly to "be accessible."

## Install (Claude.ai / Cowork)

Package the folder into a `.skill` file and upload it via **Settings → Capabilities → Skills**
(or drag-and-drop where skill upload is supported):

```bash
python -m scripts.package_skill accessible-semantic-html
```
(`scripts/package_skill.py` ships with Anthropic's `skill-creator` skill.)

## Verifying it works

Try a prompt like:

> "Build me a settings panel with a dark mode toggle and a modal for confirming account deletion."

Claude should reach for native `<dialog>`, manage focus on open/close, and use
`prefers-color-scheme` / `light-dark()` for the theme toggle — see `evals/evals.json`
for more test prompts and what to look for in the output.

## Updating

Edit `SKILL.md` or the files under `references/`, then re-copy (or re-package) into
your skills directory. Keep `SKILL.md` itself lean — put deep pattern references
in `references/` so they're only loaded into context when actually needed.
