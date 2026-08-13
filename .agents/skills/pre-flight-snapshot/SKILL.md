---
name: pre-flight-snapshot
description: Creates a rollback point (VM snapshot) prior to performing major maintenance or upgrades on a Proxmox VM.
---
## Prerequisites
- Proxmox CLI tool (`qm`) or Proxmox API / MCP server access.

## Instructions
When instructed to perform updates, maintenance, or destructive changes on a VM ($VMID):

1. **Verify VM Existence and State**:
   - Query Proxmox to check if VM `$VMID` is running or stopped:
     ```bash
     qm status $VMID
     ```

2. **Generate Unique Snapshot Name**:
   - Construct a snapshot name formatted with the current timestamp:
     `auto-pre-maint-YYYYMMDD-HHMM`

3. **Execute Snapshot Creation**:
   - Run the snapshot command with a descriptive comment:
     ```bash
     qm snapshot $VMID "auto-pre-maint-$(date +%Y%m%d-%H%M)" --description "Automated pre-maintenance checkpoint" --vmstate 0
     ```

4. **Verify Snapshot Success**:
   - List VM snapshots to confirm the checkpoint was recorded:
     ```bash
     qm listsnapshot $VMID
     ```
   - **CRITICAL**: Do NOT proceed with system upgrades if snapshot creation fails.