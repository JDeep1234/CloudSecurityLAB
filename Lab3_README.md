# Lab 3 — Phishing Detection (Google Colab)

Documentation for a phishing detection ML project.

## 🔍 Overview
This lab trains ML models (Decision Tree, Random Forest) using URL lexical features to classify phishing vs. benign URLs.

## 📊 Features Extracted
- URL length  
- Number of dots  
- Hyphen count  
- Presence of `@`  
- Use of HTTPS  
- IP-based URLs detection  

## ⚙️ Environment Setup (Colab)
```python
!pip install pandas numpy scikit-learn joblib matplotlib seaborn
```

## 📚 Models Used
- Decision Tree  
- Random Forest (100 trees)

## 📈 Evaluation
Outputs:
- Accuracy score  
- Classification report  

## 💾 Saving the Model
```python
joblib.dump(model, "phishing_model.pkl")
```

## 📝 License
MIT
