---
name: find-printer
description: "Auto-discover the HP DeskJet 5200 on the local network using ARP scan and IPP probing. Updates the printer config with the discovered IP. Run this on first use or if the printer IP has changed."
---

# Find Printer

Discover the HP DeskJet 5200 on the local network and update `context/printer-config.md` with the correct IP.

## Strategy

1. **Check current config** — read `context/printer-config.md` for the current IP.
2. **Test current IP** — try `ipptool` against the configured IP. If it responds, confirm and stop.
3. **ARP scan** — if the current IP doesn't respond, scan the local subnet:
   ```bash
   # Get the local subnet
   ip route | grep -oP 'src \K[\d.]+' | head -1
   # ARP scan (prefer arp-scan if available, fall back to nmap -sn)
   sudo arp-scan --localnet 2>/dev/null || nmap -sn 192.168.1.0/24 2>/dev/null || arp -a
   ```
4. **Identify HP device** — look for MAC prefixes belonging to HP Inc (common prefixes: `00:1A:1E`, `3C:D9:2B`, `58:20:B1`, `6C:C2:17`, `80:CE:62`, `C8:D9:D2`, `D4:61:DA`, `F4:39:09`) or hostnames containing `HP` or `DeskJet`.
5. **Verify via IPP** — for each candidate, probe port 631:
   ```bash
   ipptool -tv ipp://CANDIDATE_IP:631/ipp/print /tmp/hp5200-probe.test 2>&1 | grep -i "DeskJet"
   ```
   The IPP test file:
   ```
   {
   OPERATION Get-Printer-Attributes
   GROUP operation-attributes-tag
   ATTR charset attributes-charset utf-8
   ATTR naturalLanguage attributes-natural-language en
   ATTR uri printer-uri $uri
   ATTR keyword requested-attributes printer-make-and-model
   }
   ```
6. **Update config** — once found, update `context/printer-config.md` with the new IP and IPP URI.
7. **Update CUPS** — if the CUPS printer is pointing at the wrong IP:
   ```bash
   lpadmin -p HP-DeskJet-5200 -v ipp://NEW_IP:631/ipp/print -E
   ```

## Output

Report the discovered IP, confirm CUPS is updated, and print a one-line summary.

## If not found

Tell the user:
- Ensure the printer is powered on and connected to the same network
- Check if the printer has a static IP or DHCP reservation
- The user can manually set the IP in `context/printer-config.md`
