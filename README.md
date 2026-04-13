# Claude HP DeskJet 5200 Plugin

Claude Code plugin for HP DeskJet 5200 (Ink Advantage) printer and scanner operations over the network via IPP and eSCL.

## Why share this?

This is a deliberately simple example of a local AI agent skill — the kind of thing you'd normally just set up for yourself and never bother publishing. That's exactly why it's worth sharing. Most real-world agent skills aren't grand orchestration pipelines; they're small, practical automations that let an AI agent interact with the physical world around you. If you're looking for a template for writing your own hardware-integration skills for Claude Code, this is a good starting point.

It also demonstrates **templated variable injection** — the print templates use `«VARIABLE»` placeholders that the agent fills in at runtime with its own identity, the current timestamp, ink levels, and user content. This pattern is reusable for any skill that needs to generate structured documents.

## Skills

| Skill | Description |
|---|---|
| `find-printer` | Auto-discover the printer on your LAN using ARP scan + IPP probing |
| `check-ink` | Query ink cartridge levels (tri-color and black) via IPP |
| `print-color` | Color print job — from file, free-form content, or template |
| `print-bw` | Monochrome print job (saves color ink) — same input options |
| `scan` | Trigger a flatbed scan over the network using eSCL/AirScan |
| `test-print` | Print a sarcastic test page with ink levels, agent identity, and emoji |

## Templates

The `templates/` directory contains Typst templates with `«VARIABLE»` placeholders that the agent injects at print time:

| Template | File | Use case |
|---|---|---|
| Session Summary | `session-summary.typ` | End-of-session recap of what was accomplished |
| Blocker | `blocker.typ` | Document a blocker that needs human attention |
| Note To Self | `note-to-self.typ` | Quick reminder or thought worth printing |

### Template variables

All templates support these variables:

| Variable | Description | Example |
|---|---|---|
| `«AGENT_NAME»` | Name of the AI agent | Claude Code |
| `«MODEL_NAME»` | Model powering the agent | Opus 4.6 |
| `«DATE»` | Current date and time | 13 April 2026, 15:30 |
| `«TITLE»` | Document title | Sprint 12 Wrap-Up |
| `«BODY»` | Main content | (free-form Typst markup) |

All templates include a footer with agent name, model, date, and page numbers.

### Adding your own templates

Drop a `.typ` file in `templates/` using the same `«VARIABLE»` convention. The print skills will automatically find it.

## Setup

The printer IP is stored in `context/printer-config.md`. On first use, run `/find-printer` to auto-detect it, or edit the config file manually.

### Requirements

- CUPS (`lp`, `lpstat`, `lpadmin`)
- `ipptool` (for ink level queries)
- `sane-airscan` + `scanimage` (for scanning)
- `typst` (for document creation)
- Network access to the printer on port 631 (IPP) and 443/80 (eSCL)

## Installation

```bash
claude plugins install danielrosehill/Claude-HP5200-Skill-plugin
```

## License

MIT
