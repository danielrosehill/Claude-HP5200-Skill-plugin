# Claude HP DeskJet 5200 Plugin

Claude Code plugin for HP DeskJet 5200 (Ink Advantage) printer and scanner operations over the network via IPP and eSCL.

## Why share this?

This is a deliberately simple example of a local AI agent skill — the kind of thing you'd normally just set up for yourself and never bother publishing. That's exactly why it's worth sharing. Most real-world agent skills aren't grand orchestration pipelines; they're small, practical automations that let an AI agent interact with the physical world around you. If you're looking for a template for writing your own hardware-integration skills for Claude Code, this is a good starting point.

## Skills

| Skill | Description |
|---|---|
| `find-printer` | Auto-discover the printer on your LAN using ARP scan + IPP probing |
| `check-ink` | Query ink cartridge levels (tri-color and black) via IPP |
| `print-color` | Send a color print job (from file or generated Typst document) |
| `print-bw` | Send a monochrome print job (saves color ink) |
| `scan` | Trigger a flatbed scan over the network using eSCL/AirScan |
| `test-print` | Print a sarcastic test page to verify the printer works |

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
