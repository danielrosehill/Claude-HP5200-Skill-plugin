---
name: print-document
description: "Create a document according to user's instructions, format it with Typst, and print to the HP DeskJet 5200 network printer. Use when the user asks to create and print a document, make a printout, print a letter, create something for printing, or says 'print this'."
---

# Print Document

Create a formatted document from the user's instructions using Typst, then send it to the local HP DeskJet 5200 printer.

## Printer Details

The CUPS queue name is `HP-DeskJet-5200` by default (set up by the `find-printer` skill). If the user has a different queue name, substitute it everywhere below.

## Workflow

1. **Clarify** what the user wants in the document — content, tone, layout. If already clear, proceed.
2. **Write** the `.typ` source file under `~/printing/drafts/`.
3. **Compile** to PDF with `typst compile` (output PDF also in `drafts/`).
4. **Verify** the PDF content with `pdftotext <file>.pdf - | head -40`.
5. **Show the user** the extracted text and ask for approval before printing.
6. **Print** with `lp` once approved.
7. **Move** the final `.typ` and `.pdf` to `~/printing/done/` after successful printing.

## Typst Document Defaults

Use these defaults unless the user specifies otherwise:

```typst
#set page(paper: "a4", margin: (top: 2.5cm, bottom: 2.5cm, left: 2.5cm, right: 2.5cm))
#set text(font: "IBM Plex Sans", size: 11pt, lang: "en")
#set par(justify: true, leading: 0.65em)
```

- Default paper size: **A4** (override to `letter` if the user is in the US)
- Default font: **IBM Plex Sans** — fall back to sans-serif if unavailable
- For letters/formal docs, add date and sender info at the top
- For Hebrew content, use `#set text(lang: "he", dir: rtl)` and a Hebrew-capable font

## Creating the Document

Adapt the Typst template to the document type:

### Letter / Correspondence
```typst
#set page(paper: "a4", margin: 2.5cm)
#set text(font: "IBM Plex Sans", size: 11pt)
#set par(justify: true)

#align(right)[
  <Sender Name> \
  <Sender City> \
  #datetime.today().display("[day]/[month]/[year]")
]

#v(1.5cm)

Dear ...,

<body>

Sincerely, \
<Sender Name>
```

### Generic Document
```typst
#set page(paper: "a4", margin: 2.5cm)
#set text(font: "IBM Plex Sans", size: 11pt)
#set par(justify: true)

= Document Title

<body>
```

### List / Checklist
```typst
#set page(paper: "a4", margin: 2.5cm)
#set text(font: "IBM Plex Sans", size: 11pt)

= Title

#for item in (
  "Item one",
  "Item two",
) {
  [- #item]
}
```

## Compilation & Verification

```bash
mkdir -p ~/printing/drafts ~/printing/done
typst compile ~/printing/drafts/document.typ ~/printing/drafts/document.pdf
pdftotext ~/printing/drafts/document.pdf - | head -40
```

If compilation fails, read the error, fix the `.typ` source, and retry.

## Printing

Only print after user approval:

```bash
lp -d HP-DeskJet-5200 ~/printing/drafts/document.pdf
```

Common options if requested:
- Multiple copies: `lp -n 3 -d HP-DeskJet-5200 file.pdf`
- Duplex: `lp -o sides=two-sided-long-edge -d HP-DeskJet-5200 file.pdf`
- Landscape: `lp -o landscape -d HP-DeskJet-5200 file.pdf`

## Hard Rules

- **Always ask for approval** before sending to printer. Show the extracted text content first.
- **Always use A4** unless the user says otherwise.
- **Always compile and verify** before printing — never send a `.typ` file directly.
- **Keep source files** in `~/printing/drafts/` while iterating; move to `~/printing/done/` after successful printing.
- If the user provides a pre-existing file (PDF, text, image), skip Typst and print directly with `lp`.
