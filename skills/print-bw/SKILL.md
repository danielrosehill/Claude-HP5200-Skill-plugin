---
name: print-bw
description: "Send a black-and-white (monochrome) print job to the HP DeskJet 5200. Saves color ink by forcing grayscale output. Use when the user wants to print in B&W, grayscale, or monochrome."
---

# Print (Black & White)

Send a monochrome print job to the HP DeskJet 5200. Forces grayscale to conserve the tri-color cartridge.

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
lp -d HP-DeskJet-5200 -o print-color-mode=monochrome FILE_PATH
```

This forces the printer to use only the black cartridge, which is useful when:
- Tri-color ink is low (check with `/check-ink`)
- The document doesn't need color
- The user wants to save color ink

Common options the user may request:
- Multiple copies: `-n 3`
- Duplex: `-o sides=two-sided-long-edge`
- Landscape: `-o landscape`

## Hard Rules

- **Always ask for approval** before sending to printer (show content preview first).
- **Always use A4** unless the user specifies otherwise.
- For Typst documents, always compile and verify before printing.
