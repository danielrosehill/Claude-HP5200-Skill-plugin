---
name: print-color
description: "Send a color print job to the HP DeskJet 5200. Accepts a file path or creates a document from instructions using Typst. Use when the user wants to print something in color."
---

# Print (Color)

Send a color print job to the HP DeskJet 5200.

## Read Printer Config

Read `context/printer-config.md` for the CUPS printer name and IP.

## Input Handling

### If the user provides a file path
Print it directly — skip Typst compilation for PDF, image, or text files.

### If the user provides content/instructions
1. Create a Typst document under `/tmp/hp5200-print/`:
   ```typst
   #set page(paper: "a4", margin: 2.5cm)
   #set text(font: "IBM Plex Sans", size: 11pt)
   #set par(justify: true, leading: 0.65em)

   // Content here
   ```
2. Compile: `typst compile /tmp/hp5200-print/doc.typ /tmp/hp5200-print/doc.pdf`
3. Verify: `pdftotext /tmp/hp5200-print/doc.pdf - | head -40`
4. Show the user the extracted text and ask for approval.

## Print Command

```bash
lp -d HP-DeskJet-5200 -o print-color-mode=color FILE_PATH
```

Common options the user may request:
- Multiple copies: `-n 3`
- Duplex: `-o sides=two-sided-long-edge`
- Landscape: `-o landscape`

## Hard Rules

- **Always ask for approval** before sending to printer (show content preview first).
- **Always use A4** unless the user specifies otherwise.
- For Typst documents, always compile and verify before printing.
