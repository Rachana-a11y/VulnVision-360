# 🔐 Linux Security Hardening & Compliance using OpenSCAP and Ansible

---

## 📖 Project Overview

This project demonstrates a complete security lifecycle on a Linux system using industry tools. It includes vulnerability scanning using OpenSCAP, identifying compliance gaps based on CIS benchmarks, and automating remediation using Ansible.

The goal is to improve system security posture and validate results through before-and-after compliance reports.

---

## 🧰 Tools & Technologies

* Ubuntu 24.04
* Kali Linux
* OpenSCAP
* SCAP Security Guide (ComplianceAsCode)
* Ansible
* VirtualBox
* SSH
* Python HTTP Server

---

## 📅 Week 1 – Environment Setup

### 🎯 Objective

Set up a secure testing environment.

### ⚙️ Tasks Performed

* Installed Ubuntu 24.04 in VirtualBox
* Installed Kali Linux for testing
* Configured network connectivity
* Verified communication using ping

### 📜 Commands Used

```bash
sudo apt update && sudo apt upgrade -y
ip a
ping google.com
```

### 📸 Screenshots to Include

* Ubuntu installation screen
* Network configuration
* Successful ping output

---

## 📅 Week 2 – OpenSCAP Installation & Setup

### 🎯 Objective

Install OpenSCAP and build security content.

### ⚙️ Commands Used

```bash
sudo apt install openscap-scanner openscap-utils git cmake make -y
```

### 🔧 Manual Setup (since package unavailable)

```bash
git clone https://github.com/ComplianceAsCode/content.git
cd content
mkdir build
cd build
cmake ..
make -j4
```

### 📊 Output

* Generated `ssg-ubuntu2404-ds.xml`

### ⚠️ Challenges Faced

* scap-security-guide package missing
* Dependency errors (cmake, xml tools)
* Permission denied while cloning

### ✅ Fixes

```bash
sudo apt install xsltproc libxml2-utils -y
sudo chown -R $USER:$USER ~/content
```

### 📸 Screenshots to Include

* Build process (cmake, make)
* ds.xml file created

---

## 📅 Week 3 – Vulnerability Assessment

### 🎯 Objective

Scan system using CIS benchmark.

### ⚙️ Command Used

```bash
sudo oscap xccdf eval \
--profile xccdf_org.ssgproject.content_profile_cis_level1_server \
--results results.xml \
--report report.html \
~/content/build/ssg-ubuntu2404-ds.xml
```

### 📊 Observations

* Multiple FAIL results
* Some rules showed “Not Applicable”
* SSH-related rules not detected

### ⚠️ Challenges

* Wrong file paths
* Missing CPE dictionary
* Profile mismatch
* “Not Applicable” confusion

### ✅ Fixes

```bash
sudo mkdir -p /usr/share/openscap/cpe
sudo wget https://raw.githubusercontent.com/OpenSCAP/openscap/master/cpe/openscap-cpe-dict.xml -P /usr/share/openscap/cpe/
```

### 📸 Screenshots to Include

* HTML report (FAIL results)
* Terminal scan output

---

## 📅 Week 4 – Automated Remediation using Ansible

### 🎯 Objective

Fix vulnerabilities and improve compliance automatically.

### ⚙️ Install Ansible

```bash
sudo apt install ansible -y
```

### 📄 Playbook (hardening.yml)

```yaml
- name: Basic Security Hardening
  hosts: localhost
  become: yes

  tasks:
    - name: Install SSH server
      apt:
        name: openssh-server
        state: present

    - name: Install firewall
      apt:
        name: ufw
        state: present

    - name: Enable firewall
      shell: yes | ufw enable

    - name: Allow SSH
      command: ufw allow 22

    - name: Set SSH MaxAuthTries
      lineinfile:
        path: /etc/ssh/sshd_config
        regexp: '^#?MaxAuthTries'
        line: 'MaxAuthTries 3'

    - name: Disable root login
      lineinfile:
        path: /etc/ssh/sshd_config
        regexp: '^#?PermitRootLogin'
        line: 'PermitRootLogin no'

    - name: Restart SSH
      shell: systemctl restart ssh || systemctl restart sshd
```

### ▶️ Run Playbook

```bash
ansible-playbook -i hosts hardening.yml --ask-become-pass
```

---

## 🔁 Re-Scan After Remediation

```bash
sudo oscap xccdf eval \
--profile xccdf_org.ssgproject.content_profile_cis_level1_server \
--report final-report.html \
~/content/build/ssg-ubuntu2404-ds.xml
```

---

## 📊 Week 3 vs Week 4 Comparison

| Category         | Week 3   | Week 4   |
| ---------------- | -------- | -------- |
| Firewall         | Disabled | Enabled  |
| SSH Config       | Weak     | Hardened |
| Root Login       | Allowed  | Disabled |
| Failures         | High     | Reduced  |
| Compliance Score | Low      | Improved |

---

## 🌐 How to View HTML Report

### Method 1: Python Server (Recommended)

```bash
python3 -m http.server 8000 --bind 0.0.0.0
```

Open in Windows:

```
http://<VM-IP>:8000
```

---

### Method 2: SCP Transfer

```powershell
scp user@IP:~/final-report.html .
```

---

### Method 3: SSH + Browser (if GUI installed)

```bash
firefox final-report.html
```

---

## 🎥 Adding Videos to GitHub

GitHub does not support large videos directly.

### Recommended:

* Upload to Google Drive / YouTube (Unlisted)
* Add link:

```markdown
## 🎥 Demo Video
[Watch Here](your-video-link)
```

---

## 📸 Adding Screenshots

Create folder:

```bash
mkdir images
```

Use in README:

```markdown
![Scan Result](images/week3.png)
![Final Result](images/week4.png)
```

---

## ⚠️ Challenges Faced

* Missing OpenSCAP packages
* Build failures using cmake
* Incorrect profile usage
* “Not Applicable” confusion
* SSH not installed (missing sshd_config)
* Ansible sudo permission issues
* Network issues (Bridged adapter timeout)
* Unable to access HTML from VM
* Firewall blocking ports

---

## 💡 Key Learnings

* Practical vulnerability scanning
* CIS benchmark understanding
* Automation using Ansible
* Troubleshooting real-world errors
* Importance of correct configurations

---

## ✅ Conclusion

This project demonstrates a complete security workflow:

* Identify vulnerabilities using OpenSCAP
* Fix them using Ansible automation
* Validate improvements through rescanning

It highlights the importance of automation in maintaining secure and compliant systems.

---
