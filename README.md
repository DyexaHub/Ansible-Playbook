# Ansible Playbook Repository

This repository contains a structured and modular Ansible automation setup designed for multi‑OS environments (Linux & Windows), DNS provisioning, load balancing (HAProxy), web service deployments, and environment bootstrapping at scale. Everything is separated into clean directories so you can extend, re-use, and maintain playbooks easily.

---

## 📁 Repository Structure

```text
src/
├── site/
│   ├── all-linux        # Inventory group: all Linux hosts
│   ├── site-1           # Inventory group: first site/cluster
│   └── site-2           # Inventory group: second site/cluster
│
├── templates/           # Jinja2 templates for service configs
│   ├── haproxy.cfg.j2
│   └── index.html.j2
│
└── yaml/                # Playbooks (modular tasks)
    ├── add-user.yml
    ├── apache-install.yml
    ├── dns-add-record.yml
    ├── dns-config.yml
    ├── dns-install.yml
    ├── etc-hosts-all.yml
    ├── haproxy.yml
    ├── hostname-all.yml
    ├── install-package.yml
    ├── restart-dns.yml
    ├── restart-web.yml
    ├── stop-dns.yml
    ├── windows-dns.yml
    └── windows-iis.yml
```

---

## 🚀 Purpose of This Repository

This automation suite is designed for environments that need:

* **Site‑based provisioning** (site‑1, site‑2, etc.)
* **Linux & Windows hybrid automation**
* **DNS server management** (install, configure, add/remove records)
* **Web stack provisioning** (Apache, IIS, custom index templates)
* **Load balancing** using HAProxy
* **Infrastructure bootstrap** (users, hostnames, packages, /etc/hosts)
* **Restart/Stop utilities** for services

Everything is written to be reusable, with small modular playbooks instead of one giant file.

---

## 🔧 How to Use

### 1. Run Any Playbook

```bash
ansible-playbook -i src/site/site-1 src/yaml/apache-install.yml
```

### 2. Run Against Linux or Windows Groups

```bash
ansible-playbook -i src/site/all-linux src/yaml/add-user.yml
ansible-playbook -i src/site/site-1 src/yaml/windows-iis.yml
```

### 3. Pass Extra Variables

Useful for DNS records, custom hostnames, etc.

```bash
ansible-playbook -i src/site/site-1 src/yaml/dns-add-record.yml \
  --extra-vars "dns_record=app01 dns_ip=10.10.10.21"
```

---

## 📦 Playbook Overview

### **User & System Setup**

* **add-user.yml** – Create system users
* **hostname-all.yml** – Apply hostname to all hosts
* **install-package.yml** – Install common packages
* **etc-hosts-all.yml** – Sync `/etc/hosts` on all hosts

### **Web Services (Linux & Windows)**

* **apache-install.yml** – Install & configure Apache
* **windows-iis.yml** – Configure IIS on Windows servers
* **restart-web.yml** – Restart web services

### **DNS Automation**

* **dns-install.yml** – Install and prepare DNS server
* **dns-config.yml** – Deploy main DNS config
* **dns-add-record.yml** – Add A/AAAA/CNAME records
* **restart-dns.yml** – Apply DNS changes
* **stop-dns.yml** – Stop DNS server
* **windows-dns.yml** – Windows DNS role setup

### **Load Balancing**

* **haproxy.yml** – Install & apply HAProxy config

  * Uses template: `templates/haproxy.cfg.j2`

### **Template-Driven Web Content**

* **index.html.j2** – Custom landing page template

---

## 🧩 Inventory Structure

Each site folder contains hosts targeting different playbooks. Example:

```ini
# src/site/site-1
[web]
10.10.10.10
10.10.10.11

[dns]
10.10.10.5

[loadbalancer]
10.10.10.2
```

You can customize for multi‑datacenter, multi‑environment, or multi‑department setups.

---

## ⚙️ Example Workflow (Typical Use Case)

### 🔹 1. Provision DNS

```bash
ansible-playbook -i src/site/site-1 src/yaml/dns-install.yml
ansible-playbook -i src/site/site-1 src/yaml/dns-config.yml
```

### 🔹 2. Add Application Records

```bash
ansible-playbook -i src/site/site-1 src/yaml/dns-add-record.yml \
  --extra-vars "dns_record=backend01 dns_ip=10.10.10.41"
```

### 🔹 3. Deploy Web Servers

```bash
ansible-playbook -i src/site/site-1 src/yaml/apache-install.yml
```

### 🔹 4. Deploy Load Balancer

```bash
ansible-playbook -i src/site/site-1 src/yaml/haproxy.yml
```

### 🔹 5. Post‑Deployment Utilities

```bash
ansible-playbook -i src/site/site-1 src/yaml/restart-web.yml
```

---

## 🛡️ Best Practices

* Keep playbooks small & modular ✔️
* Use templates for repeatable configs ✔️
* Group hosts by role (web/dns/lb) ✔️
* Maintain consistent variable names ✔️
* Use `--check` mode before applying changes:

```bash
ansible-playbook ... --check --diff
```

---

## ❤️ Contribution Guide

Feel free to extend:

* More service modules
* Additional templates
* Multi-site load balancing
* Windows automation improvements

Follow the current directory structure to keep everything clean.

---

## 📜 License

MIT or your preferred open-source license.

---

If you want, I can also generate:

* Full variable structure example (group_vars/host_vars)
* HAProxy sample topology diagram
* DNS architecture reference
* CI/CD pipeline for linting + testing playbooks
