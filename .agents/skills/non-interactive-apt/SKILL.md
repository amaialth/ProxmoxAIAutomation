---
name: non-interactive-apt
description: Performs safe, non-blocking Debian/Ubuntu package updates and autoremove routines inside guest VMs using non-interactive flags. Use when asked to update OS packages on guest VMs.
---

## Prerequisites
- SSH access or Proxmox QEMU Guest Agent execution tool (`qm guest exec`).

## Instructions
When executing package updates inside a guest Linux VM:

1. **Set Non-Interactive Environment Flags**:
   - Always prepend `export DEBIAN_FRONTEND=noninteractive` to prevent interactive `dpkg` dialogs (e.g., GRUB, SSH config updates).

2. **Run Standard Maintenance Chain**:
   - Execute the update and cleanup sequence in a subshell:
     ```bash
     bash -c "export DEBIAN_FRONTEND=noninteractive; apt-get update && apt-get dist-upgrade -y -o Dpkg::Options::='--force-confdef' -o Dpkg::Options::='--force-confold' && apt-get autoremove -y && apt-get clean"
     ```

3. **Check for Reboot Requirement**:
   - Check if a reboot is required after kernel updates:
     ```bash
     test -f /var/run/reboot-required && echo "REBOOT_REQUIRED" || echo "NO_REBOOT_NEEDED"
     ```
   - Report to the user if a reboot is required, but **do not auto-reboot** unless explicitly requested.