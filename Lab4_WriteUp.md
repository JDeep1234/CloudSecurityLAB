# Lab 4: Cloud Security Monitoring Dashboard using AWS CloudWatch

## College Lab Write-Up

---

### **Experiment No:** 4

### **Title:** Implementation of Cloud Security Monitoring and Alerting System using AWS CloudWatch

---

### **Aim:**
To set up a comprehensive monitoring and alerting system for cloud infrastructure using AWS CloudWatch, SNS notifications, and custom dashboards.

---

### **Objectives:**
1. Configure and launch EC2 instances on AWS
2. Set up SNS topics for email notifications
3. Create CloudWatch alarms for CPU utilization monitoring
4. Build visualization dashboards for metrics
5. Validate the alerting system by simulating high resource usage

---

### **Theory:**

**AWS CloudWatch:**
Amazon CloudWatch is a monitoring and observability service for AWS resources and applications. It collects metrics, logs, and events, providing a unified view of AWS resources, applications, and services.

**CloudWatch Metrics:**
Metrics are time-ordered data points representing the values of a variable over time. AWS services automatically send metrics to CloudWatch. Common EC2 metrics include:
- CPUUtilization
- NetworkIn/NetworkOut
- DiskReadOps/DiskWriteOps
- StatusCheckFailed

**CloudWatch Alarms:**
Alarms watch metrics and trigger actions when thresholds are breached. Alarm states include:
- **OK:** Metric is within threshold
- **ALARM:** Metric has breached threshold
- **INSUFFICIENT_DATA:** Not enough data to determine state

**Amazon SNS (Simple Notification Service):**
SNS is a fully managed pub/sub messaging service for application-to-application and application-to-person communication. It supports multiple protocols including email, SMS, HTTP, and Lambda.

**EC2 (Elastic Compute Cloud):**
Amazon EC2 provides scalable computing capacity in the AWS cloud. Users can launch virtual servers (instances) with various configurations.

---

### **Software/Hardware Requirements:**

| Component | Specification |
|-----------|---------------|
| AWS Account | Free Tier eligible |
| Operating System | Windows 10/11, Linux, or macOS |
| AWS CLI | Version 2 |
| Python | Version 3.x |
| boto3 Library | AWS SDK for Python |
| SSH Client | For EC2 connection |
| EC2 Key Pair | .pem file for authentication |

---

### **Algorithm/Procedure:**

**Step 1: AWS CLI Configuration**
1. Install AWS CLI v2
2. Run `aws configure`
3. Enter Access Key ID and Secret Access Key
4. Set default region (us-east-1)
5. Set output format (json)

**Step 2: Launch EC2 Instance**
1. Navigate to EC2 in AWS Console
2. Click Launch Instance
3. Select Amazon Linux 2 AMI (Free Tier)
4. Choose t3.micro instance type
5. Create or select key pair
6. Configure security group (allow SSH, HTTP)
7. Launch and note Public IP

**Step 3: Connect to EC2**
1. Use SSH with key pair
2. Connect: `ssh -i key.pem ec2-user@<IP>`

**Step 4: Install Stress Tool**
1. Run: `sudo dnf install stress -y`
2. This tool simulates CPU load

**Step 5: Create SNS Topic**
1. Run Python script (create_sns_topic.py)
2. Create topic: SecurityAlertsTopic
3. Subscribe email endpoint
4. Confirm subscription from inbox

**Step 6: Create CloudWatch Alarm**
1. Run Python script (create_cloudwatch_alarm.py)
2. Configure alarm parameters:
   - Metric: CPUUtilization
   - Threshold: > 70%
   - Period: 300 seconds
   - Actions: Send to SNS topic

**Step 7: Create Dashboard**
1. Open CloudWatch Console
2. Create new dashboard
3. Add CPU Utilization widget
4. Add other relevant metrics

**Step 8: Validate System**
1. Run stress test: `stress --cpu 2 --timeout 300`
2. Observe metrics in dashboard
3. Verify email alert received

---

### **Program/Code:**

*(Refer to the detailed implementation in [README_Lab4_AWS_CloudWatch.md](./README_Lab4_AWS_CloudWatch.md))*

**Key Code Snippets:**

```python
# create_sns_topic.py
import boto3

sns_client = boto3.client("sns", region_name="us-east-1")

def create_sns_topic():
    topic_name = "SecurityAlertsTopic"
    response = sns_client.create_topic(Name=topic_name)
    topic_arn = response["TopicArn"]
    
    # Subscribe email
    sns_client.subscribe(
        TopicArn=topic_arn,
        Protocol="email",
        Endpoint="your-email@gmail.com"
    )
    return topic_arn
```

```python
# create_cloudwatch_alarm.py
import boto3

cloudwatch_client = boto3.client("cloudwatch", region_name="us-east-1")

def create_alarm(instance_id, sns_topic_arn):
    cloudwatch_client.put_metric_alarm(
        AlarmName=f"HighCPU-{instance_id}",
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
```

---

### **Expected Output:**

**Console Output:**
```
SNS topic created: arn:aws:sns:us-east-1:123456789:SecurityAlertsTopic
Subscription request sent to xyz@gmail.com. Please confirm from your inbox.
CloudWatch Alarm created: HighCPU-i-0123456789abcdef
```

**Dashboard View:**
- CPU Utilization graph showing spike during stress test
- Alarm state changing from OK to ALARM

**Email Alert:**
```
Subject: ALARM: "HighCPU-i-0123456789abcdef" in US East (N. Virginia)

Alarm Details:
- Name: HighCPU-i-0123456789abcdef
- State: ALARM
- Reason: Threshold Crossed: 1 datapoint [95.5] > 70.0
```

---

### **Result:**
The AWS CloudWatch monitoring system successfully:
1. Launched and configured EC2 instance
2. Created SNS topic with email subscription
3. Set up CloudWatch alarm for CPU monitoring
4. Built visualization dashboard
5. Triggered alarm during stress test
6. Delivered email notification when threshold exceeded

---

### **Conclusion:**
This experiment demonstrated the implementation of a cloud security monitoring system using AWS services. Key learnings include:
- CloudWatch provides comprehensive monitoring for AWS resources
- SNS enables real-time notifications for security events
- Alarms can automate incident response through various actions
- Dashboards provide visual insights into system health
- Proactive monitoring helps identify and respond to security threats quickly
- AWS Free Tier allows hands-on learning without significant costs

---

### **Viva Questions:**

1. **What is AWS CloudWatch?**
   - CloudWatch is AWS's monitoring service that collects metrics, logs, and events from AWS resources and applications.

2. **What is the difference between CloudWatch Metrics and Logs?**
   - Metrics are numerical time-series data points. Logs are text-based records of events and activities.

3. **What is Amazon SNS?**
   - Simple Notification Service is a pub/sub messaging service for sending notifications via email, SMS, HTTP, etc.

4. **What are the three states of a CloudWatch Alarm?**
   - OK (within threshold), ALARM (threshold breached), INSUFFICIENT_DATA (not enough data).

5. **What is the purpose of the stress tool?**
   - The stress tool generates synthetic CPU, memory, or I/O load for testing system behavior under stress.

6. **How does CloudWatch help in security monitoring?**
   - CloudWatch monitors metrics for anomalies, triggers alarms for suspicious activity, and integrates with other AWS security services.

---

### **References:**
1. AWS CloudWatch Documentation: https://docs.aws.amazon.com/cloudwatch/
2. Amazon SNS Documentation: https://docs.aws.amazon.com/sns/
3. AWS EC2 Documentation: https://docs.aws.amazon.com/ec2/
4. boto3 Documentation: https://boto3.amazonaws.com/v1/documentation/api/latest/
