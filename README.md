# Zabbix Monitoring with Docker Compose

Welcome to the **Zabbix Monitoring** repository! This guide will help you understand, deploy, and manage Zabbix using Docker Compose. Whether you're a beginner or experienced user, this README provides a complete step-by-step manual.

---

## ⚙️ Table of Contents

1. [What is Zabbix?](#-what-is-zabbix)
2. [Why Use Zabbix?](#-why-use-zabbix)
3. [Advantages & Disadvantages of Zabbix](#%EF%B8%8F-advantages--disadvantages-of-zabbix)
4. [Zabbix vs Other Paid Monitoring Tools](#%EF%B8%8F-zabbix-vs-other-paid-monitoring-tools)
5. [Open Source Alternatives to Zabbix](#-open-source-alternatives-to-zabbix)
6. [Zabbix Installation Methods](#-zabbix-installation-methods)
7. [Run Zabbix using Docker Compose](#%EF%B8%8F-run-zabbix-using-docker-compose)
8. [Accessing Zabbix Dashboard](#-accessing-zabbix-dashboard)
9. [Accessing phpMyAdmin](#-accessing-phpmyadmin)
10. [Useful Resources](#-useful-resources)
11. [Credits & References](#-credits--references)

---

## ❓ What is Zabbix?

**Zabbix** is a powerful open-source monitoring solution for networks, servers, virtual machines, cloud services, containers, and more. It offers real-time monitoring, performance metrics, and alerting capabilities.

---

## 💡 Why Use Zabbix?

✅ Free and open-source (no license fee)
✅ Scalable for small to large infrastructures
✅ Rich visualization (graphs, maps, dashboards)
✅ Auto-discovery of devices/services
✅ Real-time alerting and notifications
✅ Supports SNMP, IPMI, JMX, HTTP, Agent-based & Agentless monitoring
✅ Suitable for enterprise-grade monitoring



---

## ✔️ Advantages & Disadvantages of Zabbix

### Advantages:

* No license cost
* Active community support
* Support for SNMP, IPMI, JMX, agent, and agentless monitoring
* Web interface and customizable widgets

### Disadvantages:

* Steeper learning curve for beginners
* Configuration can be complex
* Initial setup requires time and planning

---

## 🏛️ Zabbix vs Other Paid Monitoring Tools

| Feature           | Zabbix   | Datadog   | New Relic | Nagios XI |
| ----------------- | -------- | --------- | --------- | --------- |
| Open Source       | Yes      | No        | No        | Partial   |
| Price             | Free     | Paid      | Paid      | Paid      |
| Cloud Integration | Moderate | Excellent | Excellent | Moderate  |
| Customization     | High     | Medium    | Medium    | Medium    |
| Community Support | Large    | Limited   | Limited   | Moderate  |

---

## 🪡 Open Source Alternatives to Zabbix

1. **Prometheus** – Excellent time-series metrics & alerting
2. **Netdata** – Lightweight, real-time performance monitoring
3. **Nagios Core** – Popular infrastructure monitoring tool
4. **Checkmk (Raw Edition)** – Scalable enterprise monitoring
5. **Icinga** – Fork of Nagios with modern UI

### Zabbix vs Prometheus:

* Zabbix is good for full-stack infrastructure monitoring with graphs and UI
* Prometheus is better for containerized environments and Kubernetes

---

## 🚀 Zabbix Installation Methods

* Manual installation (Linux packages or source)
* Using pre-built virtual appliances
* Using cloud images (AWS, Azure)
* **Using Docker Compose** (Fast and reproducible)

---

## ⚙️ Run Zabbix using Docker Compose

### Prerequisites:

* Docker & Docker Compose installed on your system

### Steps:

1. **Clone the Repository**:

```bash
git clone https://github.com/SumonPaul18/zabbix-monitoring.git
cd zabbix-monitoring
```

2. **Run Docker Compose**:

```bash
docker-compose up -d
```

3. **Verify Containers**:

```bash
docker ps
```

Containers:

* `zabbix-db`: MySQL backend
* `zabbix-server`: Zabbix backend
* `zabbix-web`: Frontend
* `zabbix-agent`: Optional local agent
* `phpmyadmin`: Web-based MySQL admin

---

## 📅 Accessing Zabbix Dashboard

* Open browser: `http://localhost:8080`
* Default credentials:

  * **Username**: Admin
  * **Password**: zabbix

---

## 🔐 Accessing phpMyAdmin

* Open browser: `http://localhost:8081`
* Use:

  * **Server**: zabbix-db
  * **Username**: root
  * **Password**: root\_password

---

## 🔗 Useful Resources

* [Zabbix Documentation](https://www.zabbix.com/documentation/current/manual)
* [Zabbix GitHub Docker Repo](https://github.com/zabbix/zabbix-docker)
* [Zabbix on Kubernetes](https://github.com/bezarsnba/zabbix-on-kubernetes.git)
* [Zabbix on Kubernetes](https://github.com/monitoringartist/kubernetes-zabbix.git)
* [Zabbix Docker](https://github.com/monitoringartist/zabbix-docker-monitoring)
* [Zabbix YouTube Guide](https://youtu.be/ScKlF0ICVYA)

---

## 👨‍💻 Credits & References

* [Zabbix on Kubernetes by monitoringartist](https://github.com/monitoringartist/kubernetes-zabbix.git)
* [Zabbix on Docker by zabbix](https://github.com/zabbix/zabbix-docker.git)
* [Zabbix Docker Monitoring by monitoringartist](https://github.com/monitoringartist/zabbix-docker-monitoring)

---

> ✨ Maintained by **Suman Pal** | Contributions are welcome!
