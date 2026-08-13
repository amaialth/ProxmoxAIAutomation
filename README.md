# Proxmox MCP & AI Agent Maintenance Framework

An intelligent, agentic maintenance framework for **Proxmox Virtual Environment (PVE)** and guest Linux Virtual Machines. Powered by the **Model Context Protocol (MCP)**, this project enables AI assistants (such as Cursor, Claude Desktop, Copilot Agent Mode, or Roo Code) to autonomously inspect, manage, and execute routine maintenance across your hypervisor and guest instances safely and effectively.

---

## 🌟 Key Features

- **Proxmox Cluster Management**: Direct integration with Proxmox VE REST API and QEMU Guest Agent channels for VM lifecycle management, snapshots, and node health checks.
- **Modular Agent Skill Library**: On-demand, lazy-loaded skill definitions (`.md`) equipped with safety guardrails and automated rollback protections.
- **Sequential Thinking Integration**: Built-in step-by-step reasoning and dynamic revision loops to prevent unexpected side effects on production systems.
- **Non-Interactive Execution Safeguards**: Configured with explicit `DEBIAN_FRONTEND=noninteractive` flags and dpkg option overrides to prevent command hanging on terminal prompts.

---

## 📁 Repository Structure

```text
ProxmoxMCP/
├── .agent/
│   ├── proxmox-host-maintainer.md     # Primary agent persona for Proxmox node maintenance
│   ├── guest-vm-maintenance.md        # Agent persona for guest Linux VM maintenance
│   └── skills/                        # Lazy-loaded skill library
│       ├── pre-flight-snapshot/       # Creates VM snapshots prior to destructive tasks
│       │   └── SKILL.md
│       ├── non-interactive-apt/       # Safe, non-blocking APT update/upgrade routine
│       │   └── SKILL.md
│       ├── journal-vacuum/            # Systemd log pruning and log rotation
│       │   └── SKILL.md
│       ├── docker-prune/              # Docker image, container, and cache cleanup
│       │   └── SKILL.md
│       ├── fstrim-storage/            # Thin-provisioned SSD storage TRIM optimization
│       │   └── SKILL.md
│       └── sequential-thinking/       # Multi-step reasoning and hypothesis verification
│           └── SKILL.md
├── .gitignore                         # Excludes node_modules, credentials, and temp files
└── README.md                          # Project documentation
```

---

## 🛠️ Prerequisites

1. **Proxmox VE Cluster** (7.x / 8.x) with active VMs/containers.
2. **QEMU Guest Agent** installed and running on target VMs:
   ```bash
   sudo apt update && sudo apt install -y qemu-guest-agent
   sudo systemctl enable --now qemu-guest-agent
   ```
   *(Note: Target VMs must undergo a full cold shutdown/power-on from Proxmox after enabling QEMU Guest Agent in VM Options).*
3. **Node.js / npx** or **Python / uvx** installed on your workstation.
4. An MCP-compatible AI client (e.g., Cursor, Claude Desktop, Roo Code / Cline, or Copilot Agent Mode).

---

## ⚙️ Configuration & Setup

### 1. Agent Configuration (`.agent/proxmox-host-maintainer.md`)

```yaml
---
name: Proxmox Host Maintainer
description: Agent for maintaining Proxmox VE hypervisor host health, storage, updates, and troubleshooting.
tools:
  - 'proxmox-local/*'         # Proxmox MCP endpoints
  - 'sequential-thinking/*'   # Sequential thinking tool
---
```

### 2. Add MCP Server Configuration

Add your server endpoints to your AI client configuration file (`claude_desktop_config.json`, `mcp.json`, or Cursor Settings):

#### Direct local MCP (Recommended for OS Maintenance)
```json
{
  "mcpServers": {
    "proxmox-local": {
      "command": "bun",
      "args": ["run", "PATH_TO/index.ts", "--stdio"],
      "env": {
        "PROXMOX_HOST": "IP_ADDRESS",
        "PROXMOX_USER": "root@pam",
        "PROXMOX_TOKEN_NAME": "mcp",
        "PROXMOX_TOKEN_VALUE": "API_TOKEN",
        "PROXMOX_VERIFY_SSL": "false"
      }
    }
  }
}
```
You can also run the server and use http to access the MCP server.
---

## 🚀 Usage & Prompt Examples

Once connected, invoke the agent in your AI workspace by referencing the agent files or asking natural language questions:

### 1. Proxmox Host Health & Node Maintenance
> *"Use the Proxmox Host Maintainer agent to inspect cluster load, trim ZFS storage pools, and vacuum journal logs older than 7 days."*

### 2. Full VM Upgrade with Pre-Flight Rollback Snapshot
> *"Run pre-flight snapshots on VM 100 and VM 102, then perform full OS package updates and clear the APT cache."*

### 3. Container & Disk Storage Optimization
> *"Check Docker disk usage on VM 100, prune unused containers and images, and run fstrim across all mounted filesystems."*

---

## 🔒 Security Best Practices

- **Never Commit Secrets**: Do not commit API tokens, passwords, or private SSH keys into Git. Keep credentials isolated in local `.env` files or environment variables.
- **Least Privilege Access**: Create dedicated Proxmox API Tokens (`root@pam!mcp`) restricted to specific paths (e.g., `/vms/100`) rather than sharing unrestricted root credentials.
- **Pre-Flight Checkpoints**: Always enable the `pre-flight-snapshot` skill before allowing AI agents to run distribution upgrades (`apt dist-upgrade`).

---

## 🤝 Contributing

1. Fork the Repository
2. Create a Feature Branch (`git checkout -b feature/new-skill`)
3. Commit your changes (`git commit -m "feat: add new agent skill for backup validation"`)
4. Push to the Branch (`git push origin feature/new-skill`)
5. Open a Pull Request

---

## 📜 License

Distributed under the [MIT License](LICENSE).