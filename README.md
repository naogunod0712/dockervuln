# DevSecOps Pipeline: Automated DAST & Developer Remediation Engine

## 📌 Executive Summary
This project demonstrates an automated DevSecOps workflow designed to perform Dynamic Application Security Testing (DAST) on an intentionally vulnerable web server ([OWASP Juice Shop](https://hub.docker.com/r/bkimminich/juice-shop)) running inside Docker. 

Beyond standard vulnerability reporting, this project features a **Developer Remediation Engine** that extracts vulnerability fixes from the OWASP ZAP scan artifacts and posts them directly as a sub-comment on the generated GitHub Issue.

---

## 🛠️ Tools & Technologies Used

* **Target Application:** OWASP Juice Shop (Containerized via Docker)
* **CI/CD Orchestration:** GitHub Actions (Multi-job workflow architecture)
* **DAST Scanner:** OWASP ZAP (Zed Attack Proxy - Baseline Scan)
* **JSON Parser & CLI:** `jq` & GitHub CLI (`gh`)
* **Reporting & Alerting:** GitHub Issues & Artifact Management

---

## 🏗️ Workflows & Architecture Comparison

The repository includes two separate GitHub Action workflow configurations to showcase workflow evolution:

1. **`vulnapp.yml` (Single-Job Baseline):** Runs the core DAST scan against the running container.
2. **`vulnapp-decoupled.yml` (Multi-Job Remediation Architecture):** Decouples the security scan from the remediation processing to overcome pipeline lifecycle constraints.

### The Problem Solved (Decoupling Rationale)
During initial testing with a single-job workflow, posting a remediation sub-comment failed. The OWASP ZAP Action creates or updates the target GitHub Issue at the very end of its execution phase. Attempting to comment on the issue in a subsequent step within the *same* job failed because the issue number did not exist yet in the GitHub repository.

**The Solution:** Decoupling the pipeline into two dependent jobs (`needs: zap-scan-job`). 
* **Job 1** handles container execution, scanning, artifact upload, and initial GitHub Issue creation.
* **Job 2** runs sequentially after Job 1 finishes, downloads the scan artifacts, queries GitHub for the newly opened issue, extracts developer solutions from JSON using `jq`, and posts an actionable remediation guide as a sub-comment.
