---
name: print-color
description: "Send a color print job to the HP DeskJet 5200. Accepts a file path, free-form content, or a named template (session-summary, blocker, note-to-self). Use when the user wants to print something in color."
---

# Print (Color)

Send a color print job to the HP DeskJet 5200.

## Read Printer Config

Read `context/printer-config.md` (in this plugin's directory) for the CUPS printer name and IP.

## Locate Plugin Directory

This skill may be invoked from any repository. To find the templates and config, locate the plugin's installed path:

```bash
PLUGIN_DIR=$(find ~/.claude/plugins/cache/danielrosehill/hp5200-printer -maxdepth 1 -type d | sort -V | tail -1)
```

## Input Handling

### 1. User provides a file path
Print it directly — no Typst compilation needed for PDF, image, or text files.

### 2. User provides content or instructions
Create a Typst document from scratch under `/tmp/hp5200-print/`.

### 3. User requests a template
Use one of the templates from `$PLUGIN_DIR/templates/`. Available templates:

| Template | File | Use case |
|---|---|---|
| Session Summary | `session-summary.typ` | End-of-session recap |
| Blocker | `blocker.typ` | Document a blocker for human attention |
| Note To Self | `note-to-self.typ` | Quick printed reminder or thought |

#### Template variable injection

All templates use `«VARIABLE»` placeholders. Before compiling, replace them:

| Variable | Value |
|---|---|
| `«AGENT_NAME»` | The AI agent's name (e.g. "Claude Code") |
| `«MODEL_NAME»` | The model (e.g. "Opus 4.6") — use whatever model you actually are |
| `«DATE»` | Current date/time formatted as "13 April 2026, 15:30" |
| `«TITLE»` | Document title — ask the user or infer from context |
| `«BODY»` | The main content — from user input or generated |

To apply:
```bash
cp "$PLUGIN_DIR/templates/session-summary.typ" /tmp/hp5200-print/doc.typ
sed -i 's|«AGENT_NAME»|Claude Code|g' /tmp/hp5200-print/doc.typ
sed -i 's|«MODEL_NAME»|Opus 4.6|g' /tmp/hp5200-print/doc.typ
# ... etc for each variable
```

Or write the file directly with variables already substituted (preferred — avoids sed escaping issues).

## Compile and Print

```bash
mkdir -p /tmp/hp5200-print
typst compile /tmp/hp5200-print/doc.typ /tmp/hp5200-print/doc.pdf
pdftotext /tmp/hp5200-print/doc.pdf - | head -40
```

Show the user the extracted text and ask for approval, then:

```bash
lp -d HP-DeskJet-5200 -o print-color-mode=color /tmp/hp5200-print/doc.pdf
```

Common options:
- Multiple copies: `-n 3`
- Duplex: `-o sides=two-sided-long-edge`
- Landscape: `-o landscape`

## Hard Rules

- **Always ask for approval** before sending to printer (show content preview first). Exception: test-print skill.
- **Always use A4** unless the user specifies otherwise.
- **Always compile and verify** before printing — never send a `.typ` file directly.
- If the user provides a pre-existing file (PDF, image, text), skip Typst and print directly.
