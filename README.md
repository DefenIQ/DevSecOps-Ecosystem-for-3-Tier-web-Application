# DevSecOps-Pipeline for 3 Tier web Application
it is a fully automated infrastructure-as-code (IaC) environment deployed on AWS. We use Terraform to build the servers, HashiCorp Vault to manage secrets like "digital keys," and the ELK Stack to act as our 24/7 monitoring. Most importantly, it features an Automated Incident Response (AIR) engine that detects and kills threats.

![WhatsApp Image 2026-01-28 at 2 54 06 PM](https://github.com/user-attachments/assets/1cbefbb1-0a85-4616-be5d-8c9dcfed6759)
\# Self-Healing DevSecOps Pipeline for 3-Tier Web Applications

## Key Features
* **Automated Infrastructure**: Provisioning of a 4-node AWS environment using Terraform.
* **Continuous Integration/Deployment**: Automated pipeline orchestrated by Jenkins with GitHub webhook integration.
* **Shift-Left Security**: Infrastructure scanning with Checkov and application dependency scanning with Trivy.
* **Multi-Layered Defense**: Comprehensive protection using ModSecurity (WAF), Suricata (IDS/IPS), and Falco (Runtime Security).
* **Centralized Observability**: Unified log management using the ELK Stack (Elasticsearch, Logstash, Kibana) with Filebeat log shipping.
* **Self-Healing Automation**: Custom scripts that automatically block malicious IPs and restart compromised services via cron-scheduled verification.

## Architecture
The application follows a standard 3-tier model, with each layer isolated and secured:

* **Frontend Tier**: Nginx reverse proxy and protected by ModSecurity WAF.
* **Backend Tier**: Application logic layer with Suricata and Falco monitoring.
* **Database Tier**: Persistence layer with network and runtime security controls.
* **Management Tier**: Hosts the Jenkins CI/CD server and the centralized ELK Stack.

## Tech Stack
* **Cloud**: AWS (EC2, VPC, Security Groups).
* **Orchestration**: Jenkins, GitHub Actions.
* **Infrastructure as Code**: Terraform.
* **Security Tools**: ModSecurity, Falco (Modern eBPF), Suricata, Checkov, Trivy.
* **Log Management**: Elasticsearch, Logstash, Kibana, Filebeat.
* **Automation**: Bash Scripting, Python, crontab, iptables.

## Setup and Installation

### Prerequisites
* AWS Account with programmatic access.
* Terraform 1.0+ installed.
* Jenkins LTS server configured.
* Docker and Docker Compose installed (for ELK Stack).

### 1. Infrastructure Provisioning
Navigate to the `infra` directory and initialize the environment:

```bash
terraform init
terraform plan
terraform apply
```

### 2. CI/CD Pipeline Setup
* Configure a new Pipeline job in Jenkins.
* Link the project GitHub repository.
* Define the Jenkinsfile path and set up webhooks for automated triggers on push events.

### 3. Deploying the ELK Stack
On the Management Server, navigate to the ELK directory and start the containers:

```bash
docker-compose up -d
```
```bash
sudo sysctl -w vm.max_map_count=262144
```

## Security Operations

### Automated Monitoring
The system monitors security logs every 2 minutes. The `verify-security.sh` script parses JSON alerts from ModSecurity, Falco, and Suricata to identify high-severity threats.

### Self-Healing Responses
* **Drop Action**: Immediate permanent blocking via `iptables` for confirmed malicious activity, such as SQL Injection.
* **Quarantine Action**: Temporary 1-hour IP block with automated rollback for suspicious activity.
* **Service Recovery**: Automated restart of the Falco or Suricata services if they are detected as inactive.

### Whitelist Protection
A configuration file is maintained to ensure that management IPs, internal network ranges, and localhost are never blocked by automated actions, preventing accidental self-lockouts.

## Audit and Compliance
All automated actions are recorded for forensic analysis:

* `/var/log/security-audit.log`: Records all drop and quarantine actions.
* `/var/log/security-rollback.log`: Specifically tracks the release of quarantined IPs.

Centralized dashboards in Kibana provide real-time visualization of attack trends and system responses.

## Authors

| Name | Profile Links |
| :--- | :--- |
| **Manan Kansagra** | [![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/MananKansagra) [![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/manankansagra/) |
| **Om Barde** | [![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/0mBarde) [![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/om-barde-499b14223/) |
| **Anurag Kurulkar** | [![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/AnuragKurulkar) [![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/anuragkurulkar/) |
| **Vaibhavi Lugade** | [![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/Vslugade) [![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/vaibhavi-lugade-78273b220) |
