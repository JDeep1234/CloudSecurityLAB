# Google Drive Encryptor — AES + Drive API

README for a Python encryption tool integrating AES + Google Drive API.

## 🔐 Features
- AES‑EAX encryption/decryption  
- Upload encrypted files to Google Drive  
- Download and decrypt  
- OAuth 2.0 token caching  

## 📦 Requirements
```
google-api-python-client
google-auth-httplib2
google-auth-oauthlib
pycryptodome
tqdm
```

## 📁 Project Structure
```
project/
├── credentials.json
├── auth.py
├── crypto_utils.py
├── main.py
└── token.pickle
```

## ⚙️ Usage
```bash
python main.py
```

## 📝 License
MIT
