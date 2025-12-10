# Lab 3: Detection of Phishing Websites Using Cloud-hosted Data Mining

## 📋 Overview

Implement **phishing website detection** using **machine learning** in a cloud-hosted environment (Google Colab). This lab demonstrates how to use data mining techniques to identify phishing sites by extracting URL features and training classification models.

## 🎯 Learning Objectives

- Set up a cloud-based machine learning environment using Google Colab
- Perform data collection and preprocessing for URL datasets
- Extract lexical features from URLs for phishing detection
- Train and evaluate Decision Tree and Random Forest classifiers
- Deploy and test a phishing detection model

## 🛠️ Prerequisites & Requirements

| Component | Minimum Requirement | Notes |
|-----------|---------------------|-------|
| Google Colab | Free Google Account | Run notebook in browser |
| Python | 3.8+ | Default in Colab |
| Libraries | pandas, numpy, scikit-learn, matplotlib, seaborn, joblib | Install via pip |
| Dataset | Phishing + Legitimate URLs | CSV format (Kaggle/PhishTank/Mendeley) |

## 🚀 Setup Instructions

### Step 1: Environment Setup

Install required libraries in Colab:

```python
# Run this first
!pip install -q pandas numpy scikit-learn joblib

# Optional visualization
!pip install -q matplotlib seaborn
```

### Step 2: Data Collection & Upload

Download open datasets:
- **Phishing URLs**: [Mendeley Dataset](https://data.mendeley.com/datasets/vfszbj9b36)
- **Legitimate URLs**: Available on PhishTank or Kaggle

Upload CSVs to your Colab instance or fetch with Python:

```python
from google.colab import files
# files.upload()  # uncomment to invoke upload dialog
```

## 📁 Project Structure

```
Colab Notebook/
├── phishing-urls.csv        # Phishing URL dataset
├── legitimate-urls.csv      # Legitimate URL dataset
├── phishing_rf_model.pkl    # Saved trained model
└── notebook.ipynb           # Main Colab notebook
```

## 📝 Code Implementation

### 1. Data Preprocessing

```python
import os
import pandas as pd

phishing_path = "phishing-urls.csv"
legit_path = "legitimate-urls.csv"

def load_csv(path):
    if not os.path.exists(path):
        raise FileNotFoundError(f"{path} not found.")
    df = pd.read_csv(path, dtype=str, low_memory=False)
    return df

phishing = load_csv(phishing_path)
legit = load_csv(legit_path)

def find_url_col(df):
    candidates = [c for c in df.columns if c.lower() in
        ('url', 'link', 'website', 'website_url', 'uri', 'domain', 'site')]
    if candidates:
        return candidates[0]
    for c in df.columns:
        sample = df[c].dropna().astype(str).head(200).str.lower()
        if sample.str.contains('http').sum() > 5 or sample.str.contains('www').sum() > 5:
            return c
    return df.columns[0]

phishing = phishing.rename(columns={find_url_col(phishing): 'url'})[['url']].dropna().reset_index(drop=True)
legit = legit.rename(columns={find_url_col(legit): 'url'})[['url']].dropna().reset_index(drop=True)

phishing['label'] = 1  # Phishing
legit['label'] = 0     # Legitimate

data = pd.concat([phishing, legit], ignore_index=True).sample(frac=1, random_state=42).reset_index(drop=True)
```

### 2. Feature Engineering

Extract lexical features from URLs:

```python
import re

def extract_features(url):
    return [
        len(url),                                                    # URL length
        url.count('.'),                                              # Number of dots
        url.count('-'),                                              # Number of hyphens
        int('@' in url),                                             # Contains @ symbol
        int('https' in url[:8].lower()),                             # Uses HTTPS
        int(re.search(r'[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+', url) is not None)  # Has IP address
    ]

X = data['url'].apply(extract_features)
X = pd.DataFrame(X.tolist(), columns=['length', 'dots', 'hyphens', 'has_at', 'has_https', 'has_ip'])
y = data['label']
```

### 3. Train-Test Split

```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.3, random_state=42, stratify=y
)
```

### 4. Model Training — Decision Tree

```python
from sklearn.tree import DecisionTreeClassifier

dt = DecisionTreeClassifier(random_state=42)
dt.fit(X_train, y_train)
```

### 5. Model Training — Random Forest

```python
from sklearn.ensemble import RandomForestClassifier

rf = RandomForestClassifier(n_estimators=100, random_state=42)
rf.fit(X_train, y_train)
```

### 6. Model Evaluation

```python
from sklearn.metrics import classification_report, accuracy_score

for name, model in [("Decision Tree", dt), ("Random Forest", rf)]:
    preds = model.predict(X_test)
    print(f"\n{name}")
    print("Accuracy:", accuracy_score(y_test, preds))
    print(classification_report(y_test, preds))
```

### 7. Save/Load Model

```python
import joblib

# Save model
joblib.dump(rf, "phishing_rf_model.pkl")

# Load model
model = joblib.load("phishing_rf_model.pkl")
```

### 8. Test with Sample URLs

```python
test_urls = [
    "http://secure.paypal.com.fake-site/login",
    "https://www.google.com"
]

test_features = pd.DataFrame([extract_features(u) for u in test_urls])
preds = rf.predict(test_features)
print(list(zip(test_urls, preds)))
# Output: [('http://secure.paypal.com.fake-site/login', 1), ('https://www.google.com', 0)]
```

### 9. Visualization (Optional)

```python
import matplotlib.pyplot as plt
import seaborn as sns

sns.histplot(data['url'].apply(len), bins=50)
plt.title("Distribution of URL Lengths")
plt.xlabel("URL Length")
plt.ylabel("Count")
plt.show()
```

## ▶️ Running the Project

1. Open Google Colab and create a new notebook
2. Upload the phishing and legitimate URL datasets
3. Run cells sequentially to:
   - Load and preprocess data
   - Extract features
   - Train models
   - Evaluate performance
4. Test with custom URLs
5. Download trained model for deployment

## ✅ Validation & Testing Checklist

- [ ] Dataset loaded and labeled correctly
- [ ] Feature extraction works for all URLs
- [ ] Models trained without errors
- [ ] Accuracy and classification report printed
- [ ] Test URLs predicted correctly (phishing=1, legitimate=0)
- [ ] Visualizations generated

## 🔧 Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| Model accuracy low | Weak features | Add domain-based / lexical features |
| Colab FileNotFound | Wrong dataset path | Upload via `files.upload()` or mount Drive |
| Memory error | Large dataset | Sample data or use smaller subset |
| Import errors | Missing libraries | Run `!pip install` commands |

## 📊 Features Explained

| Feature | Description | Phishing Indicator |
|---------|-------------|-------------------|
| `length` | Total URL length | Longer URLs often phishing |
| `dots` | Number of `.` in URL | More dots = suspicious |
| `hyphens` | Number of `-` in URL | Excessive hyphens suspicious |
| `has_at` | Contains `@` symbol | Often used in phishing |
| `has_https` | Uses HTTPS protocol | Legitimate sites use HTTPS |
| `has_ip` | Contains IP address | Phishing often uses IPs |

## 🔗 Resources

- **Google Colab Notebook**: [Open in Colab](https://colab.research.google.com/drive/1npFZqH0kTH4kR-Do8vdAyt20-OnNdgXt?usp=sharing)
- **Mendeley Dataset**: https://data.mendeley.com/datasets/vfszbj9b36
- **PhishTank**: https://phishtank.org

## 🚀 Deployment Options

- **Google Colab**: Interactive notebook, share via link or "File > Save a copy in Drive"
- **Local Deployment**: Download `phishing_rf_model.pkl` and use in any Python application
- **Web API**: Wrap model in Flask/FastAPI for REST endpoint
