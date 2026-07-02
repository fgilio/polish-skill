# polish skill

A code-review skill for Claude Code that hunts for **simplification**. It analyzes a scope end to end, convenes Taylor Otwell, DHH, and Adam Wathan as parallel reviewer personas (Caleb Porzio joins when frontend files are in scope), and synthesizes their findings into one prioritized list. The bar is semantic clarity: code that stays obvious six months from now and under production pressure.

Everything lives in [SKILL.md](skills/polish/SKILL.md).

## Modes

One skill, two modes — selected by argument:

- **Report mode (default)** — `/polish` analyzes and reports the prioritized findings. Writes no code.
- **Interactive mode** — `/polish --interactive` walks each finding with you via questions, assembles a plan together, and implements what you confirm.

Optionally scope the review:

```
/polish --scope=changes      # default
/polish --scope=branch
/polish --interactive --scope=project
```

## Installation

### As a plugin

Installs through the [fgilio marketplace](https://github.com/fgilio/claude-plugins) and receives updates as the skill evolves:

```
/plugin marketplace add fgilio/claude-plugins
/plugin install polish@fgilio
```

### Manual clone

Clone and symlink into your Claude Code skills directory:

```bash
git clone https://github.com/fgilio/polish-skill.git ~/dev/skills/polish-skill
ln -s ~/dev/skills/polish-skill/skills/polish ~/.claude/skills/polish
```

This path also works with any agent that supports the open [Agent Skills](https://agentskills.io) format. Updates require a manual `git pull`.

## License

[MIT](LICENSE)
