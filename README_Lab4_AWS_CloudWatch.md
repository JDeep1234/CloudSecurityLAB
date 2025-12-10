# Lab 4: Cloud Security Monitoring Dashboard using AWS CloudWatch

## 📋 Overview

Set up a **monitoring and alerting system** for a cloud application using **AWS CloudWatch**. This lab includes monitoring EC2 instance performance metrics (CPU utilization), configuring alerts through SNS, visualizing metrics in a dashboard, and validating alerts by simulating high CPU usage.

## 🎯 Learning Objectives

- Configure AWS CLI for programmatic access
- Launch and manage EC2 instances
- Create SNS topics and email subscriptions for alerts
- Set up CloudWatch alarms for CPU monitoring
- Build CloudWatch dashboards for visualization
- Simulate and validate alert triggers

## 🛠️ Prerequisites & Requirements

| Component | Requirement | Notes |
|-----------|-------------|-------|
| AWS Account | Free Tier | Sign up at aws.amazon.com |
| AWS CLI | v2 | [Download MSI Installer (Windows 64-bit)](https://aws.amazon.com/cli/) |
| Python | 3.x | For running scripts |
| EC2 Key Pair | `.pem` file | Created in AWS Console |
| Knowledge | Basic AWS services | EC2, CloudWatch, SNS |

## 🚀 Setup Instructions

### Step 1: Configure AWS CLI

```bash
aws configure
```

Enter your credentials when prompted:
```
AWS Access Key ID: <your-access-key>
AWS Secret Access Key: <your-secret-key>
Default region name: us-east-1
Default output format: json
```

### Step 2: Launch EC2 Instance

1. In AWS Console, search for **EC2**
2. Click **Launch Instance** → Enter name: `Security-Monitoring-Instance`
3. **AMI**: Select Amazon Linux 2 AMI (Free Tier)
4. **Instance type**: Choose `t3.micro` (Free Tier eligible)
5. **Key pair**:
   - Select "Create new key pair"
   - Download `.pem` file and save securely
6. **Network settings**:
   - Enable Auto-assign Public IP
   - Allow SSH (22), HTTP (80), HTTPS (443)
7. **Storage**: Leave at default (8GB)
8. Click **Launch Instance**
9. Copy **Public IPv4 address** when running

### Step 3: Connect to EC2 Instance

```bash
ssh -i Pair1.pem ec2-user@<EC2-Public-IP>
```

### Step 4: Install Stress Tool

Inside the EC2 instance:
```bash
sudo dnf install stress -y
```

### Step 5: Run Stress Test

Simulate CPU load for 5 minutes:
```bash
stress --cpu 2 --timeout 300
```

## 📁 Project Structure

```
VSCode_AWS_Monitoring/
├── create_sns_topic.py         # SNS topic creation script
├── create_cloudwatch_alarm.py  # CloudWatch alarm creation script
├── venv/                       # Virtual environment
└── Pair1.pem                   # EC2 Key Pair (keep secure!)
```

## 📝 Code Files

### create_sns_topic.py - SNS Topic Creation

```python
import boto3

# Initialize SNS client
sns_client = boto3.client("sns", region_name="us-east-1")

def create_sns_topic():
    topic_name = "SecurityAlertsTopic"

    # Create SNS topic
    response = sns_client.create_topic(Name=topic_name)
    topic_arn = response["TopicArn"]
    print(f"SNS topic created: {topic_arn}")

    # Subscribe your email (update with your email ID)
    email = "xyz@gmail.com"
    sns_client.subscribe(
        TopicArn=topic_arn,
        Protocol="email",
        Endpoint=email
    )
    print(f"Subscription request sent to {email}. Please confirm from your inbox.")

    return topic_arn

if __name__ == "__main__":
    create_sns_topic()
```

### create_cloudwatch_alarm.py - CloudWatch Alarm Creation

```python
import boto3

# Initialize CloudWatch client
cloudwatch_client = boto3.client("cloudwatch", region_name="us-east-1")

def create_alarm(instance_id, sns_topic_arn):
    alarm_name = f"HighCPU-{instance_id}"

    response = cloudwatch_client.put_metric_alarm(
        AlarmName=alarm_name,
        ComparisonOperator="GreaterThanThreshold",
        EvaluationPeriods=1,
        MetricName="CPUUtilization",
        Namespace="AWS/EC2",
        Period=300,
        Statistic="Average",
        Threshold=70.0,
        ActionsEnabled=True,
        AlarmActions=[sns_topic_arn],
        AlarmDescription="Alarm when CPU exceeds 70%",
        Dimensions=[
            {"Name": "InstanceId", "Value": instance_id}
        ]
    )
    print(f"CloudWatch Alarm created: {alarm_name}")

if __name__ == "__main__":
    # Replace with your values
    instance_id = "i-12345"
    sns_topic_arn = "arn:aws:sns:us-east-1:123456789:SecurityAlertsTopic"
    create_alarm(instance_id, sns_topic_arn)
```

## ▶️ Running the Project

### Step 6: Create SNS Topic & Subscription

```bash
python create_sns_topic.py
```
**⚠️ Important**: Confirm the subscription from your email inbox!

### Step 7: Create CloudWatch Alarm

```bash
python create_cloudwatch_alarm.py
```

This creates an alarm that triggers when CPU utilization exceeds 70%.

### Step 8: Create CloudWatch Dashboard (Console)

1. Go to **AWS Console → CloudWatch → Dashboards**
2. Create a new dashboard
3. Add widgets:
   - CPU Utilization
   - Memory (if CloudWatch Agent installed)
   - Disk metrics

### Step 9: Verify Alerts & Results

1. Re-run the stress command on EC2:
   ```bash
   stress --cpu 2 --timeout 300
   ```
2. Observe CPUUtilization graph in the dashboard
3. Verify email alert is received when threshold is crossed

## ✅ Validation & Testing Checklist

- [ ] CPUUtilization metrics displayed in CloudWatch dashboard
- [ ] CloudWatch Alarm triggered when CPU > 70%
- [ ] Email notification received from SNS
- [ ] Dashboard successfully visualized system activity

## 📊 Expected Results

| Component | Expected Behavior |
|-----------|------------------|
| **EC2 Stress Test** | CPU spikes to ~100% |
| **CloudWatch Metrics** | Graph shows CPU increase |
| **CloudWatch Alarm** | Triggers when CPU > 70% |
| **SNS Email** | Alert notification received |

## 🔧 Troubleshooting

| Issue | Solution |
|-------|----------|
| SSH connection refused | Check security group allows port 22; verify key pair |
| AWS CLI not configured | Run `aws configure` with valid credentials |
| No email received | Check spam folder; confirm SNS subscription |
| Alarm not triggering | Verify instance ID matches; check threshold settings |
| Stress command not found | Run `sudo dnf install stress -y` |

## 📚 Quick Commands Reference

```bash
# Check Python version
python --version

# Activate virtual environment (Windows)
.\venv\Scripts\Activate

# Configure AWS CLI
aws configure

# Run SNS topic script
python create_sns_topic.py

# Create CloudWatch alarm
python create_cloudwatch_alarm.py

# Connect to EC2
ssh -i Pair1.pem ec2-user@<EC2-Public-IP>

# Install stress tool on EC2
sudo dnf install stress -y

# Run CPU stress test (5 minutes)
stress --cpu 2 --timeout 300
```

## 🔒 Security Best Practices

- **Never commit** AWS credentials to version control
- **Rotate** access keys regularly
- **Use IAM roles** for EC2 instances when possible
- **Keep `.pem` files** secure and private
- **Monitor costs** to avoid unexpected charges

## 📈 Alternative: Console-Based CloudWatch Alarm

1. In **CloudWatch → Alarms → Create Alarm**
2. Select metric → **AWS/EC2 → CPUUtilization**
3. Set condition:
   - **Threshold**: Greater than 70
   - **Period**: 5 minutes
4. Configure notification:
   - Create/select SNS topic
   - Add email endpoint
5. Name alarm and create

## 🎯 Conclusion

This lab successfully demonstrates:
- Setting up cloud monitoring with AWS CloudWatch
- Creating automated alerts via SNS
- Visualizing metrics using dashboards
- Validating the system by simulating high CPU load
