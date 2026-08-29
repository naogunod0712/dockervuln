# DevSecOps Pipeline: Automated DAST & Developer Remediation Engine

## Summary
I created and worked on project to demonstrates an automated DevSecOps workflow designed to perform Dynamic Application Security Testing (DAST) on an intentionally vulnerable web server ([OWASP Juice Shop](https://hub.docker.com/r/bkimminich/juice-shop)) running inside Docker. 

Beyond standard vulnerability reporting, this project features a **Developer Remediation Engine** that extracts vulnerability fixes from the OWASP ZAP scan artifacts and posts them directly as a sub-comment on the generated GitHub Issue, allowing devs have immediate notice on how to fix issues

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

---

## 💡 Developer Experience (DX)

Instead of forcing developers to read through raw log files or heavy report zips:
1. **Automated Alerting:** The pipeline opens a high-level tracking issue outlining all identified vulnerability alerts.
2. **Actionable Fixes:** The remediation engine immediately adds a sub-comment titled `### 🛠️ [Vulnerability Name]` containing exact steps on **How to Fix** the security flaw, directly referencing OWASP solutions.

---

## 📚 Official Documentation & References

The following official documentation was what I read to help me build this workflow

* **Container Image:** [OWASP Juice Shop on Docker Hub](https://hub.docker.com/r/bkimminich/juice-shop)
* **DAST Scanner Data:** [OWASP ZAP Traditional JSON Report Structure](https://www.zaproxy.org/docs/desktop/addons/report-generation/report-traditional-json/)
* **Issue Querying:** [GitHub CLI Manual (`gh issue list`)](https://cli.github.com/manual/gh_issue_list)
* **Pipeline Syntax:** [GitHub Actions Workflow Syntax (Jobs & Steps ID)](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax#jobsjob_idstepsid)
