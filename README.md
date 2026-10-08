# ashi-liquid-glass

A skill for AI coding agents that builds **liquid-glass UI** — frosted surfaces with real light refraction (Snell's law), two-sided rim light, and optional RGB dispersion on the edges.

## What you get

- **Two engines:** `full` (best look — 3-channel RGB edge dispersion) and `lite` (fastest — single channel + performance tricks)
- **20+ ready components:** buttons, navbar, cards, modal, sidebar, tabs, switch, slider, toast, tooltip, accordion, table, and more
- **Two paths:** apply glass onto your existing design (layout untouched), or design from scratch step by step

## How to use

This follows the open [Agent Skills](https://agentskills.io/specification) format, so it works with any compatible AI coding agent (Claude Code, Cursor, Gemini CLI, Copilot CLI, and others). Copy `SKILL.md` into your project's skills folder (or load it in your agent), then ask for a glassmorphism/liquid-glass interface. The skill guides the agent step by step: engine choice → existing-or-scratch → components → build.

## Requirements

- **Chromium-based browser** (Chrome, Edge, Arc) for the full refraction effect. Other browsers gracefully fall back to blur + rim light.

## License

MIT — free to use, modify, and share. See [LICENSE](LICENSE).
