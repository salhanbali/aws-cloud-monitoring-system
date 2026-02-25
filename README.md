# AWS Cloud Monitoring & Logging System ☁️

A production-style cloud monitoring and logging system built on AWS, designed to collect system metrics, automate alerts, and archive logs simulating real-world infrastructure monitoring.

---

## 📌 Project Overview

This project demonstrates how to build a fully functional cloud monitoring pipeline using core AWS services. The system continuously collects server metrics, triggers automated alerts when thresholds are exceeded, and stores logs in scalable cloud storage.

---

## 🏗️ Architecture

<img width="589" alt="architecture" src="https://github.com/user-attachments/assets/719489af-b480-44dc-a5bd-5963afc7e173" />


```
EC2 Instance (monitoring-server)
        │
        ▼
CloudWatch Agent
  ├── Metrics (CPU, Memory, Disk, Swap) ──► CloudWatch Metrics & Dashboard
  └── Logs (/var/log/messages) ──────────► CloudWatch Log Groups
        │
        ▼
CloudWatch Alarm (High-CPU-Alert)
        │
        ▼
SNS Topic ──► Email Notification
        │
        ▼
S3 Bucket (Log Archive) ◄── Cron Job (Hourly Sync)
```

---

## ☁️ AWS Services Used

| Service | Purpose |
|---|---|
| **EC2** | Virtual server that generates metrics and logs |
| **CloudWatch Agent** | Collects system-level metrics and log files |
| **CloudWatch Metrics** | Stores and visualizes CPU, memory, disk, and swap data |
| **CloudWatch Alarms** | Triggers alerts when CPU exceeds 80% threshold |
| **CloudWatch Dashboard** | Visual overview of all system metrics |
| **SNS (Simple Notification Service)** | Sends email alerts when alarms are triggered |
| **S3** | Archives log files with 30-day lifecycle management |
| **IAM** | Manages access control with least-privilege roles and policies |

---

## 📸 Screenshots

### Monitoring Dashboard
<img width="1071" alt="monitoring-dashboard" src="https://github.com/user-attachments/assets/002c0b8d-4bf8-4ff9-b760-16670fa83be3" />

### High CPU Alert
<img width="842" alt="high-cpu-alert" src="https://github.com/user-attachments/assets/5db7bb20-9327-4616-af4e-ddb95e1f7e8c" />

### CloudWatch Metrics
<img width="1426" alt="cloudwatch-metrics" src="https://github.com/user-attachments/assets/3ac34086-29d0-4da8-a0b4-e1e412a8a456" />

---

## 🔧 Setup & Configuration

### Prerequisites
- AWS Account (Free Tier eligible)
- AWS CLI installed and configured
- SSH key pair (.pem file)

### IAM Configuration
- Created IAM user with EC2, S3, and CloudWatch permissions
- Created IAM Role `EC2-Monitoring-Role` with:
  - `CloudWatchAgentServerPolicy`
  - `AmazonS3FullAccess`
- Attached role to EC2 instance for secure, keyless access

### EC2 Instance
- **AMI:** Amazon Linux 2023
- **Instance Type:** t3.micro (Free Tier)
- **Region:** us-east-1 (N. Virginia)
- **IAM Role:** EC2-Monitoring-Role

### CloudWatch Agent Configuration
The agent is configured to collect the following metrics every 60 seconds:

```json
{
  "metrics_collected": {
    "cpu": ["cpu_usage_idle", "cpu_usage_iowait", "cpu_usage_user", "cpu_usage_system"],
    "disk": ["used_percent", "inodes_free"],
    "diskio": ["io_time"],
    "mem": ["mem_used_percent"],
    "swap": ["swap_used_percent"]
  },
  "logs": {
    "files": ["/var/log/messages"],
    "retention_in_days": 30
  }
}
```

See the full config file: [`cloudwatch-agent-config.json`](cloudwatch-agent-config.json)

---

## 🚨 Monitoring & Alerting

### CloudWatch Alarm
- **Alarm Name:** High-CPU-Alert
- **Metric:** cpu_usage_idle
- **Threshold:** Triggers when CPU idle drops below 80% (indicating high CPU usage)
- **Action:** Sends SNS email notification

### Stress Test
Used the `stress` tool to simulate high CPU load and validate the alarm:

```bash
sudo yum install stress -y
stress --cpu 4 --timeout 300
```

This confirmed the full alerting pipeline — alarm triggered, email received. ✅

---

## 📦 Log Storage & Automation

Logs are automatically synced to S3 every hour using a cron job:

```bash
0 * * * * aws s3 sync /var/log/ s3://my-monitoring-logs-shahad-2025/logs/
```

### S3 Bucket Configuration
- Server-side encryption enabled (SSE-S3)
- All public access blocked
- Log retention managed via lifecycle rules

---

## 📊 CloudWatch Dashboard

A live dashboard was created with the following widgets:
- CPU Utilization over time
- Memory usage percentage
- Disk used percentage

---

## 💡 Key Learnings

- How to configure IAM roles to follow the principle of least privilege
- How CloudWatch Agent bridges the gap between EC2 and CloudWatch for memory/disk metrics
- How SNS integrates with CloudWatch Alarms to enable automated alerting
- How cron jobs can automate log archiving to S3
- How to simulate and validate production-level monitoring scenarios

---

## ⚠️ Cost Management

All services used are within the AWS Free Tier:
- EC2: t3.micro (750 hrs/month free)
- S3: 5GB storage free
- CloudWatch: 10 metrics, 10 alarms, 5GB logs free

Remember to **stop your EC2 instance** when not in use to avoid charges.

---

## 📁 Repository Structure

```
aws-cloud-monitoring-system/
├── README.md
├── cloudwatch-agent-config.json
└── screenshots/
    ├── architecture.png
    ├── monitoring-dashboard.png
    ├── high-cpu-alert.png
    └── cloudwatch-metrics.png
```

---

## 👩‍💻 Author

**Shahed Alhanbali**  
Cloud & DevOps Enthusiast  
[LinkedIn](https://www.linkedin.com/in/alhanbali/) | [GitHub](https://github.com/salhanbali)
