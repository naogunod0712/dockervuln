# dockervuln
# DevSecOps Pipeline: Automated DAST & Container Security Scanning

## 📌 Executive Summary
This project demonstrates an automated DevSecOps workflow designed to detect security vulnerabilities in a target web application using open-source security tools. 

Using GitHub Actions, the pipeline spins up a intentionally vulnerable web server ([OWASP Juice Shop](https://owasp.org/www-project-juice-shop/)) via Docker, verifies host reachability, and executes dynamic security tests using **OWASP ZAP** alongside container image analysis. Findings are automatically aggregated and dispatched to the repository's GitHub Issues tab for triaging.

---

## 🛠️ Tech Stack & Security Tools

* **Target Application:** OWASP Juice Shop (Hosted via Docker)
* **Orchestration & CI/CD:** Docker CLI, GitHub Actions
* **Dynamic Security Analysis (DAST):** OWASP ZAP (Zed Attack Proxy)
* **Reporting & Alerting:** GitHub Issues Automation, SARIF Reports

---

## ⚙️ Architecture & Pipeline Flow
