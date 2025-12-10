# ☁️ Cloud Security Lab

A comprehensive collection of hands-on cloud security labs covering encryption, authentication, threat detection, monitoring, and secure coding practices.

![Cloud Security](https://img.shields.io/badge/Cloud-Security-blue?style=for-the-badge)
![Labs](https://img.shields.io/badge/Labs-5-green?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Active-success?style=for-the-badge)

---

## 📚 Lab Index

| Lab | Title | Technologies | Guide | Write-Up |
|-----|-------|--------------|-------|----------|
| 1️⃣ | Encrypted Cloud Storage | Python, Google Drive API, AES | [📖 Guide](./README_Lab1_GDrive_Encrypt.md) | [📝 Write-Up](./Lab1_WriteUp.md) |
| 2️⃣ | Firebase MFA | React, Firebase Auth, SMS | [📖 Guide](./README_Lab2_Firebase_MFA.md) | [📝 Write-Up](./Lab2_WriteUp.md) |
| 3️⃣ | Phishing Detection | Python, ML, Google Colab | [📖 Guide](./README_Lab3_Phishing_Detection.md) | [📝 Write-Up](./Lab3_WriteUp.md) |
| 4️⃣ | AWS CloudWatch Monitoring | AWS, EC2, SNS, CloudWatch | [📖 Guide](./README_Lab4_AWS_CloudWatch.md) | [📝 Write-Up](./Lab4_WriteUp.md) |
| 5️⃣ | SQL Injection Prevention | Node.js, PostgreSQL, Heroku | [📖 Guide](./README_Lab5_SQL_Injection.md) | [📝 Write-Up](./Lab5_WriteUp.md) |

---

## 🔬 Lab Details

### 🔐 Lab 1: Encrypted Cloud File Storage System
**[📖 View Full Guide](./README_Lab1_GDrive_Encrypt.md)** | **[📝 Lab Write-Up](./Lab1_WriteUp.md)**

Build a Python application that encrypts files locally with **AES-128 encryption** before uploading to Google Drive using OAuth 2.0. Demonstrates secure cloud storage with client-side encryption.

**Key Concepts:**
- AES encryption/decryption
- Google Drive API integration
- OAuth 2.0 authentication
- Secure token management

---

### 🔑 Lab 2: Firebase Multi-Factor Authentication
**[📖 View Full Guide](./README_Lab2_Firebase_MFA.md)** | **[📝 Lab Write-Up](./Lab2_WriteUp.md)**

Create a React application with Firebase Authentication supporting email/password login, email verification, and **SMS-based MFA**. Users enroll phone numbers as second factors.

**Key Concepts:**
- Firebase Authentication
- Multi-factor authentication (MFA)
- reCAPTCHA verification
- Session management

---

### 🎣 Lab 3: Phishing Website Detection
**[📖 View Full Guide](./README_Lab3_Phishing_Detection.md)** | **[📝 Lab Write-Up](./Lab3_WriteUp.md)**

Implement phishing detection using **machine learning** in Google Colab. Train Decision Tree and Random Forest classifiers on URL features to identify malicious websites.

**Key Concepts:**
- Feature engineering from URLs
- Machine learning classification
- Model training & evaluation
- Cloud-based data mining

---

### 📊 Lab 4: AWS CloudWatch Security Monitoring
**[📖 View Full Guide](./README_Lab4_AWS_CloudWatch.md)** | **[📝 Lab Write-Up](./Lab4_WriteUp.md)**

Set up a monitoring and alerting system using **AWS CloudWatch**. Monitor EC2 CPU utilization, configure SNS alerts, and create visualization dashboards.

**Key Concepts:**
- AWS EC2 instance management
- CloudWatch metrics & alarms
- SNS notifications
- Security dashboard creation

---

### 💉 Lab 5: SQL Injection Prevention
**[📖 View Full Guide](./README_Lab5_SQL_Injection.md)** | **[📝 Lab Write-Up](./Lab5_WriteUp.md)**

Deploy a Node.js application on Heroku demonstrating **SQL injection vulnerabilities** and their prevention using parameterized queries. Includes both vulnerable and secure endpoints.

**Key Concepts:**
- SQL injection attack vectors
- Parameterized queries
- Secure coding practices
- Vulnerability testing with SQLMap

---

## 🛠️ Technologies Used

| Category | Technologies |
|----------|-------------|
| **Languages** | Python, JavaScript, SQL |
| **Frameworks** | React, Node.js, Express |
| **Cloud Platforms** | Google Cloud, AWS, Firebase, Heroku |
| **Security** | AES Encryption, OAuth 2.0, MFA |
| **Databases** | PostgreSQL |
| **ML Libraries** | scikit-learn, pandas, numpy |

---

## 🚀 Getting Started

1. **Clone the repository:**
   ```bash
   git clone https://github.com/JDeep1234/CloudSecurityLAB.git
   cd CloudSecurityLAB
   ```

2. **Choose a lab** from the table above

3. **Follow the detailed guide** for setup and execution

---

## 📋 Prerequisites

- Python 3.7+
- Node.js 14+
- Google Account (for Firebase, Google Cloud, Colab)
- AWS Account (for CloudWatch lab)
- Heroku Account (for SQL Injection lab)

---

## 📄 License

This project is for educational purposes.

---

## ⭐ Star this repo if you find it helpful!
