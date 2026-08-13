---
name: fstrim-storage
description: Issues TRIM commands across thin-provisioned SSD/NVMe filesystems on guest VMs and Proxmox storage pools to recover deleted space. Use during routine storage maintenance.
---
## Prerequisites
- Target filesystem must be thin-provisioned on SSD/NVMe backing storage.

## Instructions
When performing weekly storage maintenance:

1. **Guest VM TRIM (Inside Guest OS)**:
   - Reclaim unused blocks across all mounted filesystems:
     ```bash
     fstrim -av
     ```

2. **Proxmox Host Storage TRIM**:
   - Trim host ZFS or LVM-thin storage pools:
     ```bash
     fstrim -v /
     ```

3. **ZFS Pool Trim (If ZFS is used)**:
   - Trim designated ZFS storage pool:
     ```bash
     zpool trim <pool-name>
     ```