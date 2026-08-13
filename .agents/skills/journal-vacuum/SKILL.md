---
name: journal-vacuum
description: Prunes old systemd journal logs and rotated log archives on the Proxmox host or guest VMs to free up disk space. Use when cleaning up logs or resolving low storage warnings.
---
## Instructions
When performing disk cleanup or addressing low disk space alerts:

1. **Check Current Journal Disk Usage**:
   ```bash
   journalctl --disk-usage
   ```
2. Vacuum Logs by Age:
    Retain only logs from the last 7 days:
    ```bash
    journalctl --vacuum-time=7d
    ```
3. Vacuum Logs by Size (Optional Fallback):
    Cap maximum journal retention to 500MB if storage remains constrained:
    ```bash
    journalctl --vacuum-size=500M
    ```
4. Clean Rotated /var/log Archives:
    Delete old compressed log archives:
    ```bash
    find /var/log -type f \( -name "*.gz" -o -name "*.1" \) -delete
    ```