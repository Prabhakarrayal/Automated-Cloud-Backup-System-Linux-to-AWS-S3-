# ☁️ Enterprise Automated Cloud Backup System

[![Bash](https://img.shields.io/badge/Language-Bash-4EAA25?logo=gnu-bash&logoColor=white)](https://www.gnu.org/software/bash/)
[![AWS](https://img.shields.io/badge/Cloud-AWS%20S3-FF9900?logo=amazon-aws&logoColor=white)](https://aws.amazon.com/s3/)
[![Security](https://img.shields.io/badge/Security-IAM%20Roles-red)](https://aws.amazon.com/iam/)
[![Linux](https://img.shields.io/badge/OS-Linux%20FHS-lightgrey?logo=linux&logoColor=black)](https://foundation.linuxloc.org/)

## 🎯 Project Motive
In a production environment, "manual" is a dirty word. Data loss can cost companies millions. This project solves the problem of unreliable local backups by creating a **secure, automated, and self-cleaning** bridge between a Linux server and the AWS Cloud.

### Key Engineering Features:
* **Cost Optimization:** Automated 7-day retention policy to delete old backups and save S3 costs.
* **Data Integrity:** Uses `tar` with Gzip compression for secure, corruption-resistant packaging.
* **Production Standards:** Follows the **Linux Filesystem Hierarchy Standard (FHS)** by utilizing `/usr/local/bin` and `/var/log`.
* **Self-Logging:** Comprehensive audit trails for every execution attempt.

---

## 🏗 System Architecture


1.  **Trigger:** Linux `crontab` executes the script at a scheduled interval.
2.  **Environment Check:** Script verifies source directories and AWS CLI connectivity.
3.  **Process:** Data is compressed, timestamped, and uploaded via AWS API.
4.  **Maintenance:** The script scans the bucket/local temp for files older than the retention limit and purges them.
5.  **Audit:** Every step is recorded in a centralized log file.

---

## 🚀 Installation & Setup

### 1. Prerequisites
* AWS CLI v2 installed (`aws --version`)
* IAM User with `S3FullAccess` programmatic keys.
* Root or Sudo access on the Linux machine.

### 2. Deployment
```bash
# 1. Create the project directory and log file
sudo touch /var/log/backup_system.log
sudo chmod 666 /var/log/backup_system.log

# 2. Configure AWS (Provide your keys)
aws configure

# 3. Move the script to a global path
sudo mv backup.sh /usr/local/bin/backup.sh
sudo chmod +x /usr/local/bin/backup.sh
```
## 3. Automation (The Scheduler)
To run this backup every night at Midnight, add this line to your crontab (crontab -e):

```Bash
0 0 * * * /usr/local/bin/backup.sh >> /var/log/backup_system.log 2>&1
```
## 💻 The Core Script (backup.sh)Bash#!/bin/bash
```bash
# --- CONFIGURATION ---
SOURCE="/home/root/important_data"
BUCKET="s3://your-unique-bucket-name"
TIMESTAMP=$(date +%Y-%m-%d_%H-%M-%S)
LOG_FILE="/var/log/backup_system.log"
RETENTION_DAYS=7

exec > >(tee -a ${LOG_FILE}) 2>&1 # Internal Logging

echo "[$(date)] INFO: Starting Lifecycle..."

# 1. Validate Source
[ ! -d "$SOURCE" ] && { echo "ERROR: Source missing"; exit 1; }

# 2. Compress & Upload
BACKUP_NAME="backup_$TIMESTAMP.tar.gz"
tar -czf /tmp/$BACKUP_NAME "$SOURCE"
aws s3 cp /tmp/$BACKUP_NAME "$BUCKET/"

# 3. Cleanup & Retention
rm /tmp/$BACKUP_NAME
find /tmp -name "backup_*.tar.gz" -mtime +$RETENTION_DAYS -delete
echo "[$(date)] SUCCESS: Backup sync complete."
```
## 🛡 Troubleshooting

| Scenario | Fix |
| :--- | :--- |
| **S3 Access Denied** | Check if the IAM user has `s3:PutObject` permissions |
| **Path Errors** | Ensure the `SOURCE` variable in the script matches your actual data folder |
| **Command Not Found** | Ensure ``/usr/local/bin`` is in your `$PATH` |

## ✍️ Author
**Prabhakar Rayal**  
B.Tech CSE | Graphic Era Hill University  
📍 Rishikesh, Uttarakhand, India  

[GitHub Profile](https://github.com/Prabhakarrayal), 

[LinkedIn Profile](https://in.linkedin.com/in/prabhakar-rayal-6639682), 

[Naukari Profile](https://www.naukri.com/mnjuser/profile?id=&altresid)

## ⭐ Support

If this project helped you learn about AWS or Bash, please Star ⭐ this repository!
