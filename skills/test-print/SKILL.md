---
name: test-print
description: "Print a test page from the HP DeskJet 5200 with a joke message, ink levels, printer IP, agent identity, and repo URL. Demonstrates templated variable injection in a skill."
---

# Test Print

Print a humorous test document that doubles as a capability demo — showing templated variable injection, live ink level queries, and printer auto-discovery.

## Read Printer Config

Read `context/printer-config.md` for the CUPS printer name and IP.

## Gather Variables

Before generating the document, collect these values:

| Variable | How to get it |
|---|---|
| `AGENT_NAME` | The name of the AI agent running this skill (e.g. "Claude Code") |
| `MODEL_NAME` | The model powering the agent (e.g. "Opus 4.6", "Sonnet 4.6") — use whatever model you are |
| `PRINTER_IP` | From `context/printer-config.md` |
| `CUPS_NAME` | From `context/printer-config.md` |
| `INK_COLOR` | Query via IPP (see check-ink skill) — tri-color cartridge percentage |
| `INK_BLACK` | Query via IPP — black cartridge percentage |
| `TIMESTAMP` | Current date and time |

### Query ink levels

```bash
cat > /tmp/hp5200-ink.test <<'EOF'
{
OPERATION Get-Printer-Attributes
GROUP operation-attributes-tag
ATTR charset attributes-charset utf-8
ATTR naturalLanguage attributes-natural-language en
ATTR uri printer-uri $uri
ATTR keyword requested-attributes marker-names,marker-levels
}
EOF
ipptool -tv ipp://PRINTER_IP:631/ipp/print /tmp/hp5200-ink.test 2>&1 | grep marker-levels
rm -f /tmp/hp5200-ink.test
```

Parse the two integers from `marker-levels` — first is tri-color, second is black.

## Create the Document

Generate the Typst source with all variables injected:

```typst
#set page(paper: "a4", margin: 2.5cm)
#set text(font: "IBM Plex Sans", size: 11pt)

#v(3cm)

#align(center)[
  #text(size: 48pt)[🤖]

  #v(0.5cm)

  #text(size: 28pt, weight: "bold")[I Set Up A Local AI Agent]

  #v(0.4cm)

  #text(size: 20pt)[...And All It Did Was Print]

  #v(0.2cm)

  #text(size: 28pt, weight: "bold")[This Lousy Document]

  #v(0.4cm)

  #text(size: 48pt)[😄]

  #v(1.5cm)

  #line(length: 60%, stroke: 0.5pt)

  #v(0.6cm)

  #text(size: 10pt, fill: luma(80))[
    Printed by *«AGENT_NAME»* (`«MODEL_NAME»`) \
    «TIMESTAMP» \
    Printer: `«CUPS_NAME»` at `«PRINTER_IP»`
  ]

  #v(0.4cm)

  #text(size: 10pt, fill: luma(80))[
    *Ink levels at time of printing:* \
    Tri-color (CMY): «INK_COLOR»% #h(1cm) Black: «INK_BLACK»%
  ]

  #v(0.6cm)

  #text(size: 8pt, fill: luma(120))[
    If you're reading this, the printer works. \
    The AI is self-aware enough to be sarcastic. \
    We're all going to be fine. Probably.
  ]

  #v(1cm)

  #text(size: 8pt, fill: luma(150))[
    Plugin: `https://github.com/danielrosehill/Claude-HP5200-Skill-plugin`
  ]
]
```

Replace all `«VARIABLE»` placeholders with the actual gathered values before writing the file.

## Compile and Print

```bash
mkdir -p /tmp/hp5200-print
typst compile /tmp/hp5200-print/test.typ /tmp/hp5200-print/test.pdf
lp -d «CUPS_NAME» /tmp/hp5200-print/test.pdf
```

No approval needed — this is a test page, the user invoked it knowing what it does.

## Report

Tell the user the test page has been sent and to check the printer output tray. Include the ink levels in your response too.
