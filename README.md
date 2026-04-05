# VulnVision-360
# 🔐 System Security Hardening using OpenSCAP & Ansible

## 📌 Overview

This project demonstrates system security assessment and remediation using OpenSCAP and Ansible on Ubuntu 24.04. The goal is to identify vulnerabilities using CIS benchmarks and automate fixes using infrastructure as code.

---

## 🧪 Week 1: Environment Setup

### 🔧 Tasks Performed

* Installed Ubuntu 24.04 in VirtualBox
* Updated system packages

### 📜 Commands Used

```bash
sudo apt update && sudo apt upgrade -y
```

📸 Screenshot:

* Ubuntu installation screen
* Terminal showing update

---

## 🧪 Week 2: Tool Installation

### 🔧 Tasks Performed

* Installed OpenSCAP tools
* Cloned ComplianceAsCode repository

### 📜 Commands Used

```bash
sudo apt install openscap-scanner git cmake make -y
git clone https://github.com/ComplianceAsCode/content.git
cd content
mkdir build && cd build
cmake ..
make -j4
```

📸 Screenshot:

* Successful build output
* ds.xml file generated

---

## 🧪 Week 3: Security Scan using OpenSCAP

### 🔧 Tasks Performed

* Performed CIS benchmark scan

### 📜 Commands Used

```bash
sudo oscap xccdf eval \
--profile xccdf_org.ssgproject.content_profile_cis_level1_server \
--results results.xml \
--report report.html \
~/content/build/ssg-ubuntu2404-ds.xml
```

### 📊 Observations

* Many rules showed ❌ FAIL
* Some rules were ⚠️ Not Applicable (e.g., SSH not installed)

📸 Screenshot:

* HTML report showing FAIL results

---

## 🧪 Week 4: Automation using Ansible

### 🔧 Tasks Performed

* Installed Ansible
* Created playbook for system hardening

### 📜 Commands Used

```bash
sudo apt install ansible -y
mkdir ~/ansible-lab && cd ~/ansible-lab
```

### 📄 Playbook (hardening.yml)

```yaml
- name: Basic Security Hardening
  hosts: localhost
  become: yes

  tasks:
    - name: Install SSH
      apt:
        name: openssh-server
        state: present

    - name: Install UFW
      apt:
        name: ufw
        state: present

    - name: Enable firewall
      shell: yes | ufw enable

    - name: Configure SSH
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

## 🔁 Re-Scan After Fix

```bash
sudo oscap xccdf eval \
--profile xccdf_org.ssgproject.content_profile_cis_level1_server \
--report final-report.html \
~/content/build/ssg-ubuntu2404-ds.xml
```

### 📊 Result Comparison

* ❌ FAIL reduced
* ✅ PASS increased
* ⚠️ Not Applicable reduced

📸 Screenshot:

* Before vs After report

---

## 🎥 Recordings

* Week 3 scan execution video
* Week 4 Ansible execution video

---

## ⚠️ Challenges Faced

1. ❌ CMake build errors
   ✔ Fixed by installing dependencies

2. ❌ Missing ds.xml file
   ✔ Corrected build process

3. ❌ OpenSCAP CPE error
   ✔ Manually added CPE dictionary

4. ❌ Ansible localhost failure
   ✔ Fixed inventory configuration

5. ❌ SSH config file missing
   ✔ Installed openssh-server

6. ❌ Permission denied (sudo)
   ✔ Used --ask-become-pass

7. ❌ Wrong file paths
   ✔ Used absolute path

---

## 💡 Conclusion

This project demonstrates how security vulnerabilities can be identified using OpenSCAP and automatically remediated using Ansible. Automation improves efficiency, reduces manual effort, and ensures consistent system security.

---

## 🚀 Future Improvements

* Integrate with CI/CD pipelines
* Automate periodic scans
* Use advanced CIS profiles
