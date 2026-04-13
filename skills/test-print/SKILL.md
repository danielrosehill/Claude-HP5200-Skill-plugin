---
name: test-print
description: "Print a test page from the HP DeskJet 5200 with a joke message, timestamp, and the AI agent's name. Use to verify the printer is working."
---

# Test Print

Print a humorous test document to verify the HP DeskJet 5200 is working.

## Read Printer Config

Read `context/printer-config.md` for the CUPS printer name and IP.

## Create the Document

Generate the Typst source. The document should look like a certificate/broadside — centered, large text, dramatic:

```typst
#set page(paper: "a4", margin: 2.5cm)
#set text(font: "IBM Plex Sans", size: 11pt)

#v(4cm)

#align(center)[
  #text(size: 28pt, weight: "bold")[I Set Up A Local AI Agent]

  #v(0.5cm)

  #text(size: 20pt)[...And All It Did Was Print]

  #v(0.3cm)

  #text(size: 28pt, weight: "bold")[This Lousy Document]

  #v(2cm)

  #line(length: 60%, stroke: 0.5pt)

  #v(0.8cm)

  #text(size: 10pt, fill: luma(100))[
    Printed by *Claude Code* (Opus 4.6) \
    #datetime.today().display("[day] [month repr:long] [year], [hour]:[minute]") \
    via HP DeskJet 5200
  ]

  #v(0.5cm)

  #text(size: 8pt, fill: luma(150))[
    If you're reading this, the printer works. \
    The AI is self-aware enough to be sarcastic. \
    We're all going to be fine. Probably.
  ]
]
```

## Compile and Print

```bash
mkdir -p /tmp/hp5200-print
typst compile /tmp/hp5200-print/test.typ /tmp/hp5200-print/test.pdf
lp -d HP-DeskJet-5200 /tmp/hp5200-print/test.pdf
```

No approval needed for this skill — it's a test page, the user invoked it knowing what it does.

## Report

Tell the user the test page has been sent and to check the printer output tray.
