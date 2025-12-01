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

---

## 🧠 Advanced Concepts & Expansion

### 🔹 Modular Design Philosophy

This repository is built around a clean separation of concerns:

* **Inventory** defines *where* automation runs.
* **Playbooks** define *what* actions to execute.
* **Templates** define *how* services should be configured.
* **Variables** (if added) define *dynamic values* for reusable configs.

This makes the repo maintainable at scale — ideal for large infra, multi-region deployment, or hybrid Windows/Linux ecosystems.

### 🔹 Extending Playbooks

You can easily expand the stack by creating new YAML playbooks following the existing modular pattern.
Examples:

* Add Nginx support → `nginx-install.yml`
* Add MySQL/MariaDB → `mysql.yml`
* Add Kubernetes bootstrap → `k8s-init.yml`

Just drop the new file inside `src/yaml/` and call it via inventory of your choice.

### 🔹 CI/CD Integration

To integrate these playbooks into GitHub Actions or GitLab CI pipeline:

```yaml
- name: Run Ansible Playbook
  run: ansible-playbook -i src/site/site-1 src/yaml/apache-install.yml
```

You can trigger deployments automatically based on `push`, `tag creation`, or a manual workflow dispatch.

---

## 📚 Documentation & Scaling Guides

To keep this repo future-proof, here are recommended additions:

### 1. **Group Vars & Host Vars**

For more complex infra, create:

```
group_vars/
  web.yml
  dns.yml
host_vars/
  server01.yml
```

This makes your playbooks dynamic without editing YAML tasks.

### 2. **Role Migration (Optional)**

If the project grows, convert each playbook into a **role**, like:

```
roles/
  apache/
  dns/
  haproxy/
  windows-iis/
```

This is the industry standard and makes it enterprise-ready.

### 3. **Security Enhancements**

* Use Vault for passwords & secrets
* Create sudo-limited users for automation
* Enforce SSH key-based authentication

---

## 🔥 Example Real-World Deployment Flow

A full workflow in a multi-site environment:

### **Site Replication**

Run across multiple sites (DR site, backup site, etc.).

```bash
ansible-playbook -i src/site/site-1 src/yaml/dns-install.yml
ansible-playbook -i src/site/site-2 src/yaml/dns-install.yml
```

### **Full Stack Deployment**

```bash
ansible-playbook -i src/site/site-1 src/yaml/install-package.yml
ansible-playbook -i src/site/site-1 src/yaml/add-user.yml
ansible-playbook -i src/site/site-1 src/yaml/hostname-all.yml
ansible-playbook -i src/site/site-1 src/yaml/apache-install.yml
ansible-playbook -i src/site/site-1 src/yaml/haproxy.yml
```

### **Windows Hybrid Automation**

```bash
ansible-playbook -i src/site/site-1 src/yaml/windows-dns.yml
ansible-playbook -i src/site/site-1 src/yaml/windows-iis.yml
```

---

## 🧵 Troubleshooting

Common issues & fixes:

### ❗ Hosts unreachable

* Check SSH/WinRM connectivity
* Confirm inventory IPs and groups
* Validate firewall ports open

### ❗ Missing Python modules

```bash
sudo apt install python3-apt
sudo apt install python3-winrm (for Windows)
```

### ❗ Template rendering errors

Check file paths:

```
src/templates/index.html.j2
src/templates/haproxy.cfg.j2
```

---

This repository is built to be a **scalable**, **future-proof**, and **modular automation framework**. Whether you're deploying a small lab, managing multi-site enterprise infrastructure, or experimenting with hybrid Windows/Linux automation — this structure gives you the flexibility to grow without rewriting everything later.

Automation evolves fast. This repo gives you the foundation to adapt, upgrade, and expand however your infrastructure demands.
