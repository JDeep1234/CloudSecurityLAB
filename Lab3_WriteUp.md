# Lab 3: Detection of Phishing Websites Using Cloud-hosted Data Mining

## College Lab Write-Up

---

### **Experiment No:** 3

### **Title:** Detection of Phishing Websites Using Machine Learning in Cloud Environment (Google Colab)

---

### **Aim:**
To implement a machine learning-based phishing website detection system using URL feature extraction and classification algorithms in Google Colab cloud environment.

---

### **Objectives:**
1. Understand phishing attacks and their impact on cybersecurity
2. Extract lexical features from URLs for classification
3. Implement Decision Tree and Random Forest classifiers
4. Evaluate model performance using accuracy and classification metrics
5. Deploy and test the model on sample URLs

---

### **Theory:**

**Phishing:**
Phishing is a cybercrime where attackers disguise as legitimate entities to steal sensitive information like login credentials, credit card numbers, etc. Phishing websites are fake websites that mimic legitimate ones to deceive users.

**URL Feature Extraction:**
Phishing URLs often have distinctive characteristics:
- Longer URL lengths
- Excessive use of special characters (dots, hyphens)
- Presence of IP addresses instead of domain names
- Use of @ symbol to obscure actual destination
- Lack of HTTPS protocol

**Machine Learning for Phishing Detection:**
ML algorithms can learn patterns from known phishing and legitimate URLs to classify new URLs. Common algorithms include:
- **Decision Tree:** Tree-based classification using feature splits
- **Random Forest:** Ensemble of multiple decision trees for improved accuracy

**Google Colab:**
Google Colaboratory is a free cloud-based Jupyter notebook environment that provides GPU acceleration and pre-installed ML libraries, making it ideal for data science experiments.

---

### **Software/Hardware Requirements:**

| Component | Specification |
|-----------|---------------|
| Platform | Google Colab (cloud-based) |
| Browser | Chrome, Firefox, or Edge |
| Google Account | For Colab access |
| Python | 3.8+ (pre-installed in Colab) |
| Libraries | pandas, numpy, scikit-learn, matplotlib, seaborn |
| Dataset | Phishing and Legitimate URL CSV files |

---

### **Algorithm/Procedure:**

**Step 1: Environment Setup**
1. Open Google Colab
2. Install required libraries
3. Upload or mount datasets

**Step 2: Data Collection**
1. Load phishing URL dataset (label = 1)
2. Load legitimate URL dataset (label = 0)
3. Combine and shuffle datasets

**Step 3: Data Preprocessing**
1. Detect URL column in datasets
2. Standardize column names
3. Remove null values
4. Assign labels (1 for phishing, 0 for legitimate)

**Step 4: Feature Engineering**
Extract features from each URL:
1. URL length
2. Number of dots (.)
3. Number of hyphens (-)
4. Presence of @ symbol
5. HTTPS protocol usage
6. Presence of IP address

**Step 5: Train-Test Split**
1. Split data: 70% training, 30% testing
2. Use stratified sampling for balanced classes

**Step 6: Model Training**
1. Train Decision Tree classifier
2. Train Random Forest classifier (100 trees)

**Step 7: Model Evaluation**
1. Calculate accuracy scores
2. Generate classification reports
3. Compare model performances

**Step 8: Testing and Deployment**
1. Test with sample URLs
2. Save model using joblib
3. Load and use model for predictions

---

### **Program/Code:**

*(Refer to the detailed implementation in [README_Lab3_Phishing_Detection.md](./README_Lab3_Phishing_Detection.md))*

**Key Code Snippets:**

```python
# Feature Extraction
import re

def extract_features(url):
    return [
        len(url),                    # URL length
        url.count('.'),              # Number of dots
        url.count('-'),              # Number of hyphens
        int('@' in url),             # Has @ symbol
        int('https' in url[:8].lower()),  # Uses HTTPS
        int(re.search(r'[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+', url) is not None)  # Has IP
    ]
```

```python
# Model Training
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier

# Decision Tree
dt = DecisionTreeClassifier(random_state=42)
dt.fit(X_train, y_train)

# Random Forest
rf = RandomForestClassifier(n_estimators=100, random_state=42)
rf.fit(X_train, y_train)
```

```python
# Model Evaluation
from sklearn.metrics import classification_report, accuracy_score

for name, model in [("Decision Tree", dt), ("Random Forest", rf)]:
    preds = model.predict(X_test)
    print(f"{name} Accuracy:", accuracy_score(y_test, preds))
    print(classification_report(y_test, preds))
```

---

### **Expected Output:**

```
Decision Tree
Accuracy: 0.85
              precision    recall  f1-score   support
           0       0.84      0.86      0.85      1500
           1       0.86      0.84      0.85      1500
    accuracy                           0.85      3000

Random Forest
Accuracy: 0.89
              precision    recall  f1-score   support
           0       0.88      0.90      0.89      1500
           1       0.90      0.88      0.89      1500
    accuracy                           0.89      3000

Test URLs:
[('http://secure.paypal.com.fake-site/login', 1), 
 ('https://www.google.com', 0)]
```

---

### **Result:**
The machine learning models successfully:
1. Extracted 6 lexical features from URLs
2. Trained Decision Tree with ~85% accuracy
3. Trained Random Forest with ~89% accuracy
4. Correctly classified test URLs as phishing or legitimate
5. Saved trained model for future deployment

---

### **Conclusion:**
This experiment demonstrated the application of machine learning for phishing website detection in a cloud environment. Key findings include:
- URL lexical features can effectively distinguish phishing from legitimate websites
- Random Forest outperformed Decision Tree due to ensemble learning
- Cloud-based platforms like Google Colab enable easy ML experimentation
- Feature engineering is crucial for model performance
- The model can be deployed as a real-time phishing detection service

---

### **Viva Questions:**

1. **What is phishing?**
   - Phishing is a cyber attack where attackers impersonate legitimate entities to steal sensitive information through fake websites or emails.

2. **What features are commonly used for URL-based phishing detection?**
   - URL length, number of dots/hyphens, presence of @ symbol, IP address usage, HTTPS protocol, suspicious keywords.

3. **Why is Random Forest better than Decision Tree?**
   - Random Forest uses multiple decision trees and aggregates their predictions, reducing overfitting and improving accuracy.

4. **What is the purpose of train-test split?**
   - To evaluate model performance on unseen data and prevent overfitting.

5. **What is stratified sampling?**
   - Stratified sampling ensures that the proportion of each class is maintained in both training and testing sets.

6. **How can this model be deployed in production?**
   - The model can be saved using joblib and loaded into a web service (Flask/FastAPI) or browser extension for real-time URL checking.

---

### **References:**
1. scikit-learn Documentation: https://scikit-learn.org/stable/
2. Google Colab: https://colab.research.google.com
3. PhishTank Dataset: https://phishtank.org
4. Mendeley Phishing Dataset: https://data.mendeley.com/datasets/vfszbj9b36
