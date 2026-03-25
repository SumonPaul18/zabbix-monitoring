# Zabbix Ceph Cluster Monitoring Integration Guide

![Version](https://img.shields.io/badge/Zabbix-6.4%2B-blue)
![Ceph](https://img.shields.io/badge/Ceph-Quincy%2FReef-red)
![OS](https://img.shields.io/badge/OS-Linux%20(Ubuntu%2FCentOS)-green)
![Status](https://img.shields.io/badge/Status-Production%20Ready-success)

This documentation provides a step-by-step professional guide to integrating **Zabbix Monitoring System** with a **Ceph Storage Cluster**. It is designed for System Administrators and DevOps Engineers who manage infrastructure and need reliable storage monitoring.

---

## 📑 Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Step 1: Version Check & Planning](#step-1-version-check--planning)
4. [Step 2: Zabbix Agent 2 Installation](#step-2-zabbix-agent-2-installation)
5. [Step 3: Security & Permissions Configuration](#step-3-security--permissions-configuration)
6. [Step 4: Agent Configuration](#step-4-agent-configuration)
7. [Step 5: Zabbix Server Configuration](#step-5-zabbix-server-configuration)
8. [Step 6: Validation & Testing](#step-6-validation--testing)
9. [Troubleshooting](#troubleshooting)
10. [DevOps Best Practices](#devops-best-practices)
11. [References](#references)

---

## Introduction

Monitoring the health, performance, and capacity of a Ceph cluster is critical for any stateful infrastructure. This guide uses **Zabbix Agent 2** with custom `UserParameters` to execute Ceph commands and collect data. This method avoids complex socket permission issues and works easily on any Linux distribution.

---

## Prerequisites

Before starting, ensure your environment meets the following requirements:

*   **Ceph Cluster:** A running cluster (Minimum 1 MON, 1 OSD).
*   **Zabbix Server:** Installed and configured.
*   **Target Nodes:** Root or sudo access to the nodes running Ceph.
*   **Network Connectivity:** Port `10050` must be open between Zabbix Server and Ceph nodes.
*   **OS:** Ubuntu 20.04/22.04 or RHEL/CentOS 8/9.

---

## Step 1: Version Check & Planning

Always use compatible versions based on official documentation.

### 1.1 Check Current Versions
Run the following commands in the terminal:

```bash
# Check Zabbix Agent Version (if installed)
zabbix_agent2 -V

# Check Ceph Version
ceph --version

# Check OS Version
cat /etc/os-release
```

### 1.2 Official Source References
Check the latest stable versions before installation:
*   [Zabbix Download Page](https://www.zabbix.com/download)
*   [Ceph Documentation](https://docs.ceph.com/en/latest/)

> **Note:** This guide is based on **Zabbix 6.4 LTS** and **Ceph Quincy/Reef** versions.

---

## Step 2: Zabbix Agent 2 Installation

Install `zabbix-agent2` on the Ceph nodes. It is written in Go and offers better performance.

### 2.1 For Ubuntu/Debian

```bash
# Add official Zabbix repository
wget https://repo.zabbix.com/zabbix/6.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.4-1+ubuntu22.04_all.deb
sudo dpkg -i zabbix-release_6.4-1+ubuntu22.04_all.deb
sudo apt update

# Install Agent 2
sudo apt install zabbix-agent2 -y
```

### 2.2 For RHEL/CentOS/Rocky Linux

```bash
# Add official Zabbix repository
sudo rpm -Uvh https://repo.zabbix.com/zabbix/6.4/rhel/8/x86_64/zabbix-release-6.4-1.el8.noarch.rpm
sudo dnf clean all

# Install Agent 2
sudo dnf install zabbix-agent2 -y
```

### 2.3 Enable Service
```bash
sudo systemctl enable zabbix-agent2
sudo systemctl start zabbix-agent2
```

---

## Step 3: Security & Permissions Configuration

The Zabbix agent needs permission to run `ceph` commands. For security, we will allow passwordless `sudo` only for specific commands.

### 3.1 Verify Ceph Binary Path
```bash
which ceph
# Output is usually: /usr/bin/ceph
```

### 3.2 Configure Sudoers
Create a new sudoers file:

```bash
sudo visudo -f /etc/sudoers.d/zabbix
```

Add the following content and save (`:wq`):

```text
# Zabbix user can run ceph commands without password
zabbix ALL=(ALL) NOPASSWD: /usr/bin/ceph
```

> **Warning:** Do not give permission for `ALL` commands. Specify only the `/usr/bin/ceph` path for better security auditing.

### 3.3 Verify Permissions
Check if the `zabbix` user can run the command:

```bash
sudo -u zabbix sudo /usr/bin/ceph health
```
If you see `HEALTH_OK` or similar output, the configuration is correct.

---

## Step 4: Agent Configuration

Edit the agent configuration file to create custom items or `UserParameters`.

### 4.1 Edit Configuration File
File path: `/etc/zabbix/zabbix_agent2.conf`

```bash
sudo nano /etc/zabbix/zabbix_agent2.conf
```

### 4.2 Update Main Settings
Add the following lines at the end of the file or in the `UserParameter` section:

```conf
# Zabbix Server IP Configuration
Server=192.168.1.100
ServerActive=192.168.1.100
Hostname=ceph-node-01

# Ceph Custom UserParameters
# Key Format: ceph.<command_name>
UserParameter=ceph.health,sudo /usr/bin/ceph health
UserParameter=ceph.status,sudo /usr/bin/ceph status
UserParameter=ceph.osd.dump,sudo /usr/bin/ceph osd dump
UserParameter=ceph.df,sudo /usr/bin/ceph df
```

> **Tip:** You can write external scripts for complex data parsing, but calling commands directly is better for performance.

### 4.3 Restart Agent
Restart the agent after changing the configuration:

```bash
sudo systemctl restart zabbix-agent2
sudo systemctl status zabbix-agent2
```

---

## Step 5: Zabbix Server Configuration

Configure the host and link templates via the Zabbix Web Interface.

### 5.1 Create Host
1.  Go to **Data collection** > **Hosts** > **Create host**.
2.  **Host name:** `ceph-node-01` (Must match the hostname in the config file).
3.  **Interfaces:** Agent > IP Address (Ceph Node IP) > Port `10050`.
4.  **Templates Tab:** Search for `Ceph`. If no official template exists, create manual items in the next step.
5.  Click **Add**.

### 5.2 Create Manual Items (If no template)
1.  Click on the Host > **Items** Tab.
2.  **Create item** > **Name:** `Ceph Health Status`.
3.  **Type:** `Zabbix agent`.
4.  **Key:** `ceph.health` (Must match the key in the config file).
5.  **Type of information:** `Text`.
6.  **Update interval:** `30s`.
7.  Click **Add**.

*(Similarly, create items for `ceph.df` for capacity monitoring. Type of information should be `Numeric` if parsing values via script)*.

### 5.3 Setup Triggers
1.  Go to **Triggers** Tab > **Create trigger**.
2.  **Name:** `Ceph Cluster is not Healthy`.
3.  **Expression:** `{ceph-node-01:ceph.health.str()}"HEALTH_OK"` = `0` (Or not equal to 1).
4.  **Severity:** `High`.

---

## Step 6: Validation & Testing

Verify that the setup is working correctly.

### 6.1 Local Test (On Ceph Node)
```bash
zabbix_get -s localhost -k ceph.health
```
Expected Output: `HEALTH_OK`

### 6.2 Remote Test (From Zabbix Server)
```bash
zabbix_get -s <Ceph_Node_IP> -k ceph.health
```

### 6.3 Check Zabbix Dashboard
Go to **Monitoring** > **Latest data** in the Zabbix Web UI. Select the host. If data appears, monitoring is successful.

---

## Troubleshooting

| Issue | Possible Cause | Solution |
| :--- | :--- | :--- |
| `zabbix_get` command `timeout` | Firewall blocking | Check `sudo ufw allow 10050` or `firewall-cmd`. |
| `Permission denied` error | Sudoers config wrong | Check path in `/etc/sudoers.d/zabbix`. |
| No data received but command works | Agent config not reloaded | Run `systemctl restart zabbix-agent2`. |
| `Unsupported item key` | Key name mismatch | Match Key name in config file and Zabbix UI exactly. |

**Check Logs Commands:**
```bash
# Agent Log
sudo tail -f /var/log/zabbix/zabbix_agent2.log

# Server Log
sudo tail -f /var/log/zabbix/zabbix_server.log
```

---

## DevOps Best Practices

Since you are working in a private cloud lab and DevOps environment, consider the following:

1.  **Infrastructure as Code (IaC):** Write the entire configuration (Agent install, sudoers, config) in an **Ansible Playbook**. This allows you to ready monitoring for new nodes in minutes.
2.  **Low Level Discovery (LLD):** Use Zabbix LLD rules to automatically create items when Ceph OSDs or Monitor servers are added/removed.
3.  **Security Audit:** Regularly check `/var/log/secure` or `auth.log` to ensure `zabbix` user sudo usage is logged correctly.

---

## References

*   [Zabbix Official Documentation](https://www.zabbix.com/documentation/current/manual)
*   [Ceph Operations Guide](https://docs.ceph.com/en/latest/ops/)
*   [Zabbix Agent 2 Plugins](https://www.zabbix.com/documentation/current/manual/config/agents/native)

---
*Author: Sumon (IT Infrastructure & DevOps Expert)*
*Updated: October 2023*
```
