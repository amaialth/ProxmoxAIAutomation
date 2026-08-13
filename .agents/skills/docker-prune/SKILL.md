---
name: docker-prune
description: Removes stopped containers, unused Docker images, build caches, and unattached volumes. Use when performing maintenance or disk cleanup on Docker host VMs.
---
## Prerequisites
- Docker installed on the target machine.
- User must be in `docker` group or have `sudo` privileges.

## Instructions
When maintaining Docker host VMs:

1. **Inspect Disk Space Used by Docker**:
   ```bash
   docker system df
   ```
2. Vacuum Logs by Age:
    Retain only logs from the last 7 days:
    ```bash
    journalctl --vacuum-time=7d
    ```
3. Vacuum Logs by Size (Optional fallback):
    Cap maximum journal retention to 500MB if storage remains constrained:
    ```bash
    journalctl --vacuum-size=500M
    ```
4. Clean Rotated /var/log Files:
    Compress or remove old archived .gz log files:
    ```bash
    find /var/log -type f -name "*.gz" -delete
    find /var/log -type f -name "*.1" -delete
    ```