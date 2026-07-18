# Wazuh on Docker — Windows Host Security Monitoring

## Legal Notice
This project was deployed and tested entirely on a Windows machine I own and control, monitoring that same machine's own security logs. The Wazuh dashboard's default login (`admin`/`admin`) shown here is intentional and expected for a fresh local deployment.

This setup is intended strictly for a local lab/learning environment. Default credentials should never be left unchanged on any production or internet-facing deployment.

## Objective
Deploy the Wazuh security monitoring platform (indexer, manager, dashboard) via Docker Compose on Windows, then install a Wazuh agent on that same host so its own security events — logons, logoffs, privilege changes, file integrity — are collected, analyzed, and visualized in the dashboard in near real time.

## Repository Structure
```
Wazuh-Windows-Monitoring/
├── README.md      → Overview, setup, and how to reproduce the lab
├── report.md       → Full technical breakdown, findings, and risk analysis
└── screenshots/     → Evidence for each step, referenced in report.md
```

## Tools Used
- Docker Desktop (Windows)
- Wazuh (indexer, manager, dashboard) — v4.11.2
- Wazuh Windows agent
- PowerShell (Administrator, for agent installation)
- Git for Windows

## Setup
1. Clone the Wazuh Docker repo (pinned to a specific release):
   ```powershell
   git clone https://github.com/wazuh/wazuh-docker.git -b v4.11.2
   cd wazuh-docker\single-node
   ```
2. Generate TLS certificates:
   ```powershell
   docker compose -f generate-indexer-certs.yml run --rm generator
   ```
3. Start the full stack:
   ```powershell
   docker compose up -d
   ```
4. Log into the dashboard at `https://localhost` (default `admin`/`admin`).
5. Install and enroll the Windows agent, then start it:
   ```powershell
   msiexec /i wazuh-agent.msi /qn WAZUH_MANAGER="127.0.0.1" WAZUH_AGENT_NAME="Sma9t" /l*v install_log.txt
   NET START WazuhSvc
   ```
6. Confirm the agent appears in the dashboard and watch live events — see `report.md` for the full walkthrough, known issues encountered, and findings.
