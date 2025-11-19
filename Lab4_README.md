# Lab 4 — AWS CloudWatch Monitoring

A GitHub‑formatted guide to automate monitoring, alarms, SNS alerts, and dashboards for an EC2 instance.

## 🎯 Objective
Set up:
- CloudWatch metrics
- CPU utilization alarm
- SNS email notifications
- Dashboard visualizing CPU, Network, Disk

## 📦 Requirements
- AWS account
- AWS CLI configured
- EC2 instance running Amazon Linux 2

## 🔧 Steps
1. Launch EC2 instance  
2. Install stress tools  
3. Create SNS topic  
4. Create CloudWatch alarm (CPU > 70%)  
5. Create dashboard  
6. Trigger high CPU using:
```bash
stress --cpu 2 --timeout 300
```

## 🛠 Scripts
- `create_sns_topic.py`
- `create_cloudwatch_alarm.py`

## 📝 License
MIT
