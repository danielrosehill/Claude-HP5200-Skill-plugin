---
name: check-ink
description: "Check ink levels on the HP DeskJet 5200. Reports tri-color and black cartridge percentages with low-ink warnings."
---

# Check Ink Levels

Query the HP DeskJet 5200 via IPP and report cartridge levels.

## Read Printer IP

Read `context/printer-config.md` to get the current printer IP. If the file doesn't exist or the IP is blank, tell the user to run `/find-printer` first.

## Query Ink Levels

Create a temporary IPP test file and query the printer:

```bash
cat > /tmp/hp5200-ink.test <<'EOF'
{
OPERATION Get-Printer-Attributes
GROUP operation-attributes-tag
ATTR charset attributes-charset utf-8
ATTR naturalLanguage attributes-natural-language en
ATTR uri printer-uri $uri
ATTR keyword requested-attributes marker-names,marker-levels,marker-colors,marker-types,marker-low-levels,printer-supply,printer-supply-description,printer-state-reasons
}
EOF

ipptool -tv ipp://PRINTER_IP:631/ipp/print /tmp/hp5200-ink.test 2>&1
```

Replace `PRINTER_IP` with the IP from config.

## Parse and Display

Extract from the IPP response:

| Field | IPP attribute |
|---|---|
| Cartridge names | `marker-names` |
| Levels (%) | `marker-levels` |
| Low thresholds | `marker-low-levels` |
| Supply detail | `printer-supply` (parse `level=` values) |

Present results as a clear table:

```
Ink Levels — HP DeskJet 5200 (10.0.0.72)
─────────────────────────────────────────
  Tri-color (CMY):  ██████░░░░  10%  ⚠ LOW
  Black:            ███████░░░  30%
─────────────────────────────────────────
```

- Show a warning indicator when a cartridge is at or below its `marker-low-levels` threshold.
- Use simple block characters for the bar (█ for filled, ░ for empty, 10 chars wide).
- If the printer is unreachable, suggest running `/find-printer`.

## Cleanup

```bash
rm -f /tmp/hp5200-ink.test
```
