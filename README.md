#VulnVision 360
# 🔐 Linux Security Hardening & Compliance Project

### OpenSCAP • Ansible • Nmap (CIS Benchmark Implementation)

![Linux](https://img.shields.io/badge/Platform-Linux-blue)
![Ubuntu](https://img.shields.io/badge/Tested%20On-Ubuntu%2024.04-orange)
![Security](https://img.shields.io/badge/Domain-Cybersecurity-red)
![Automation](https://img.shields.io/badge/Tool-Ansible-green)
![Scanning](https://img.shields.io/badge/Tool-Nmap-blueviolet)
![Compliance](https://img.shields.io/badge/Standard-CIS%20Benchmark-yellow)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

---

## 📖 Introduction

This project showcases a **practical end-to-end Linux security workflow**.
The system starts in a basic, less secure state and is gradually improved through scanning, compliance checks, and automated remediation.

Instead of relying only on theory, this project focuses on **real command execution, troubleshooting, and validation**.

---

## 📸 Screenshots & 🎥 Demo

🔗 All screenshots and recordings:
https://drive.google.com/drive/folders/1ysm8eaWuvpso2yYmU9Ftx4yuD7bUbob4

---

# 📅 Week 1 – Environment Setup & Discovery

## 🎯 Goal

Set up lab environment and confirm connectivity.

## ⚙️ Work Done

* Installed Ubuntu 24.04 (VirtualBox)
* Configured networking
* Verified internet & system connectivity

## 💻 Commands

```bash
sudo apt update && sudo apt upgrade -y
ip a
ping google.com
```

## 📌 Outcome

System ready for scanning and testing.


* Ubuntu installation
* IP address (`ip a`)
* Successful ping

---

# 📅 Week 2 – Vulnerability Identification (Nmap)

## 🎯 Goal

Identify exposed services and possible weaknesses.

## ⚙️ Tool

* Nmap (NSE scripts)

## 💻 Commands

```bash
nmap -A <target-ip>
nmap --script vuln <target-ip>
```

## 🔍 What I Did

Due to setup limitations with OpenVAS, I used **Nmap scripting engine** to:

* Detect running services
* Identify open ports
* Check basic vulnerabilities

## 📊 Result

* Found active services
* Identified exposed ports
* Observed potential risks

## ⚠️ Limitation

* No detailed CVE scoring
* Limited vulnerability depth


<img width="738" height="332" alt="infotact-week1(1)" src="https://github.com/user-attachments/assets/bb4855f3-5da5-4ebe-be50-eb134d4811da" />

<img width="947" height="812" alt="Screenshot 2026-03-28 134722" src="https://github.com/user-attachments/assets/fc1adc49-c028-43f1-9fdf-32e954be984f" />

---

https://drive.google.com/file/d/1xG4vMyGByZKumpvjYbW1XhEE_VYM6pPa/view?usp=drive_link

* Nmap scan output
* Vulnerability script output

---

# 📅 Week 3 – Compliance Scan using OpenSCAP

## 🎯 Goal

Check system against **CIS Benchmark (Level 1 Server)**

## ⚙️ Command

```bash
sudo oscap xccdf eval \
--profile xccdf_org.ssgproject.content_profile_cis_level1_server \
--report report.html \
~/content/build/ssg-ubuntu2404-ds.xml
```

## 📊 Observations

* Several rules showed **FAIL**
* Many showed **NOT APPLICABLE**

## 🧠 Key Finding

“NOT APPLICABLE” occurred because:

➡️ SSH service was not installed
➡️ So SSH-related checks were skipped

## 📌 Outcome

* Identified misconfigurations
* Generated compliance report


<img width="890" height="581" alt="Screenshot 2026-04-01 131310" src="https://github.com/user-attachments/assets/aa9bcabe-af4a-4803-aa79-d3deda26ae57" />

---

https://drive.google.com/file/d/1KfgMco_3ij5vfrcPaQZHmQd1J1_aW6za/view?usp=drive_link


* HTML report (FAIL cases)
* Terminal scan output

---

# 📅 Week 4 – Automated Remediation (Ansible)

## 🎯 Goal

Fix security issues automatically.

## ⚙️ Steps

* Installed SSH server
* Configured SSH security
* Enabled firewall
* Applied system hardening

## ▶️ Run Playbook

```bash
ansible-playbook -i hosts hardening.yml --ask-become-pass
```

## 🔁 Re-Scan

```bash
sudo oscap xccdf eval \
--profile xccdf_org.ssgproject.content_profile_cis_level1_server \
--report final-report.html \
~/content/build/ssg-ubuntu2404-ds.xml
```

## 📊 Result

* FAIL results reduced
* Security improved
* Previously skipped rules now evaluated

<img width="582" height="671" alt="Screenshot 2026-04-05 082938" src="https://github.com/user-attachments/assets/7fcf35be-3577-4a6b-a03c-b1d0a9e9b9aa" />


<img width="1302" height="512" alt="Screenshot 2026-04-05 084302" src="https://github.com/user-attachments/assets/2140188c-02ed-424f-81dc-2da9142b922b" />



<img width="532" height="638" alt="Screenshot 2026-04-05 094213" src="https://github.com/user-attachments/assets/e09a5c6f-956f-454c-b19d-a79192f1b3f4" />

video of final result - https://drive.google.com/drive/folders/1ysm8eaWuvpso2yYmU9Ftx4yuD7bUbob4


* Ansible execution
* Final HTML report

---

# 📊 Before vs After

| Area     | Before         | After     |
| -------- | -------------- | --------- |
| SSH      | Not Installed  | Installed |
| Rules    | Not Applicable | Evaluated |
| Firewall | Disabled       | Enabled   |
| Security | Weak           | Improved  |

---

# ⚠️ Challenges Faced & How I Solved Them

## 1. OpenSCAP package missing

✔️ Built manually from GitHub

## 2. Permission denied (git clone)

✔️ Fixed using:

```bash
sudo chown -R $USER:$USER ~/content
```

## 3. CMake build errors

✔️ Installed dependencies:

```bash
sudo apt install cmake xsltproc libxml2-utils -y
```

## 4. NOT APPLICABLE confusion

✔️ Root cause: SSH not installed
✔️ Solution:

```bash
sudo apt install openssh-server -y
```

## 5. Ansible error (sshd_config missing)

✔️ Installed SSH service

## 6. Cannot open HTML report

✔️ Used Python server:

```bash
python3 -m http.server 8000 --bind 0.0.0.0
```

## 7. Cannot access from Windows

✔️ Fixed by:

* Using Bridged Adapter
* Checking IP with `ip a`
* Allowing firewall if needed

---

# 🌐 How to View HTML Report

## ✔️ Best Method

```bash
python3 -m http.server 8000 --bind 0.0.0.0
```

Then open:

```
http://<VM-IP>:8000
```

---

## ✔️ Alternative (Download to Windows)

```powershell
scp user@IP:~/final-report.html .
```

---

# 🎥 Adding Videos to GitHub

Since GitHub limits large files:

✔️ Upload videos to:

* Google Drive
* YouTube (Unlisted)

✔️ Add link:

```markdown
[Watch Demo](your-link)
```

---

# 📸 Adding Screenshots

Create folder:

```bash
mkdir images
```

Use:

```markdown
![Week 3](images/week3.png)
![Week 4](images/week4.png)
```

---

# 💡 Key Learnings

* Real-world vulnerability scanning
* CIS compliance understanding
* Automation using Ansible
* Debugging system-level issues
* Importance of proper configuration

---

# 📌 Conclusion

This project demonstrates a **complete security improvement cycle**:

➡️ Scan → Identify → Fix → Verify

It highlights how automation and compliance tools can significantly improve system security in a practical environment.

---

## ⭐ Author

Rachana

---
