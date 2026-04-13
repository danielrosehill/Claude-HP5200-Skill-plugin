---
name: scan
description: "Trigger a scan from the HP DeskJet 5200 flatbed scanner over the network using eSCL/AirScan. Saves the scanned image to a user-specified location."
---

# Scan

Trigger a network scan from the HP DeskJet 5200 using the eSCL (AirScan) protocol.

## Read Printer Config

Read `context/printer-config.md` for the printer IP.

## Prerequisites Check

Verify scanning tools are available:
```bash
which scanimage && dpkg -l | grep sane-airscan
```

If `sane-airscan` is not installed:
```bash
sudo apt install sane-airscan
```

## Discover Scanner

Check if the scanner is visible:
```bash
scanimage -L 2>&1
```

If no scanner is found, the eSCL endpoint may need manual configuration. Check/create `/etc/sane.d/airscan.conf`:
```bash
grep -q "HP DeskJet 5200" /etc/sane.d/airscan.conf 2>/dev/null || \
echo '[devices]
"HP DeskJet 5200" = http://PRINTER_IP:443/eSCL' | sudo tee -a /etc/sane.d/airscan.conf
```

Replace `PRINTER_IP` with the IP from config. Then retry `scanimage -L`.

If port 443 doesn't work, try port 80:
```
"HP DeskJet 5200" = http://PRINTER_IP:80/eSCL
```

## Scan Parameters

Ask the user what they need or use sensible defaults:

| Parameter | Default | Options |
|---|---|---|
| Format | PNG | png, jpeg, tiff, pdf |
| Resolution | 300 DPI | 75, 150, 300, 600 |
| Mode | Color | Color, Gray, Lineart |
| Source | Flatbed | Flatbed (HP 5200 has flatbed only) |

## Execute Scan

```bash
mkdir -p /tmp/hp5200-scans
scanimage -d 'DEVICE_STRING' \
  --format=png \
  --resolution 300 \
  --mode Color \
  -o /tmp/hp5200-scans/scan_$(date +%Y%m%d_%H%M%S).png
```

The `DEVICE_STRING` comes from `scanimage -L` output (e.g., `airscan:e0:HP DeskJet 5200`).

## Post-Scan

1. Report the file path and size.
2. Ask the user where to save it (default: `/tmp/hp5200-scans/`).
3. If the user wants PDF output from a PNG scan:
   ```bash
   convert /tmp/hp5200-scans/scan.png /tmp/hp5200-scans/scan.pdf
   ```
   (requires ImageMagick)

## Troubleshooting

If scanning fails:
- Ensure the document is placed on the flatbed glass
- Verify the printer is on and network-connected
- Try `sudo scanimage -L` (some systems need root for network scanners)
- Check firewall isn't blocking mDNS (port 5353) or eSCL (port 443/80)
