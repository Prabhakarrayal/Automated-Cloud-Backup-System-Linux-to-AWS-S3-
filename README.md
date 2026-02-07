# Automated Cloud Backup System (Linux-to-AWS S3)
## 🎯 Project Motive
In modern DevOps, data is the most valuable asset. Manual backups are inconsistent and local storage is vulnerable to hardware failure.

**The Goal:**

To build a fully automated, hands-off Disaster Recovery (DR) pipeline that ensures data integrity by migrating local server files to a highly durable AWS S3 environment using secure shell scripting.

## 🛠 Tech Stack & Requirements
- **Operating System:** Linux (Ubuntu/Debian)
- **Scripting:** Bash (Bourne Again Shell)Cloud Provider: Amazon Web Services (AWS)
- **Storage Service:** AWS S3 (Simple Storage Service)
- **Automation:** Cron (Linux Task Scheduler)

## 🚀 Implementation Steps
1. Environment Setup
   First, install the AWS Command Line Interface (CLI) to allow the Linux kernel to communicate with the AWS Cloud API.
```bash
  curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
  unzip awscliv2.zip
  sudo ./aws/install
```
2. Secure Configuration
   Configure security credentials using an IAM (Identity and Access Management) user with S3FullAccess policy.
```bash
   aws configure
   #Enter Access Key and Secret Key when prompted
```
3. The Backup Engine (``backup.sh``)
Create the core script that handles compression, timestamping, and transmission.
```Bash
#!/bin/bash
SOURCE="/home/root/important_data"
DEST="s3://your-unique-bucket-name"
TIMESTAMP=$(date +%Y-%m-%d_%H-%M-%S)
BACKUP_NAME="backup_$TIMESTAMP.tar.gz"

# Archiving and Uploading
tar -czf /tmp/$BACKUP_NAME $SOURCE
aws s3 cp /tmp/$BACKUP_NAME $DEST/
rm /tmp/$BACKUP_NAME # Local cleanup
```

4. Zero-Touch Automation
Schedule the script to run every night at 00:00 (Midnight) using the Crontab utility.
```Bash
crontab -e
# Add this line at the bottom:
0 0 * * * /path/to/backup.sh
```

## 📊 Expected Output & VerificationWhen 
When the script runs, it generates the following log output in the terminal:
```Plaintext
[2026-02-07 22:45:00] Starting compression of /home/root/important_data...
[2026-02-07 22:45:05] Uploading backup_2026-02-07.tar.gz to S3...
upload: /tmp/backup_2026-02-07.tar.gz to s3://your-bucket/backup_2026-02-07.tar.gz
[2026-02-07 22:45:10] SUCCESS: Backup Lifecycle Completed.
```

## 🛡 Troubleshooting & Edge Cases

I implemented the following logic to handle common production failures:


| Issue | Solution |
| --- | --- |
| AWS CLI Not Found | Ensure /usr/local/bin/aws is in your system $PATH. |
| Permission Denied | Run chmod +x backup.sh to grant execution rights. |
| S3 Access Denied | Verify IAM User has PutObject permissions for the specific bucket. |
| Large File Timeout | Use the --multipart-threshold flag in AWS CLI for files > 5GB. |


## ✍️ Author
**Prabhakar Rayal**  
B.Tech CSE | Graphic Era Hill University  
📍 Rishikesh, Uttarakhand, India  

[GitHub Profile](https://github.com/Prabhakarrayal), 

[LinkedIn Profile](https://in.linkedin.com/in/prabhakar-rayal-6639682), 

[Naukari Profile](https://www.naukri.com/mnjuser/profile?id=&altresid)

[Email](prabhakarrayalarcy@gmail.com)

## ⭐ Support
If this project helped you understand cloud automation or shell scripting, please give this repository a Star ⭐! It helps others find this resource.
