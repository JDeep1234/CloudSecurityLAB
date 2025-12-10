# Lab 1: Encrypted Cloud File Storage System (Google Drive + AES)

## 📋 Overview

A Python application that encrypts files locally with **AES encryption**, uploads them to **Google Drive** using **OAuth 2.0**, and can download & decrypt them back. This demonstrates secure cloud storage practices with client-side encryption.

## 🎯 Learning Objectives

- Implement AES encryption/decryption for files
- Integrate with Google Drive API using OAuth 2.0
- Understand secure token management and credential handling
- Practice least-privilege access with Drive API scopes

## 🛠️ Prerequisites & Requirements

| Component | Minimum Requirement | Notes |
|-----------|---------------------|-------|
| Python | 3.7+ | Check with `python --version` |
| IDE | Any IDE (VSCode, PyCharm) | Create project with virtual environment |
| Google Account | Personal Gmail or Workspace | Needed for APIs and OAuth credentials |
| APIs | Google Drive API | Must be enabled in your Google Cloud project |
| Credentials | `credentials.json` | OAuth 2.0 Desktop App JSON file |

### Required Libraries

```bash
pip install google-api-python-client google-auth-httplib2 google-auth-oauthlib pycryptodome tqdm
```

## 🚀 Setup Instructions

### Step 1: Google Cloud Console Setup

1. Go to [Google Cloud Console](https://console.cloud.google.com) and create/select a project
2. Navigate to **APIs & Services → Library** → Search for **Google Drive API** → **Enable**
3. Configure OAuth consent screen:
   - **User Type**: External (for personal Gmail) or Internal (for Workspace)
   - Add App name, Support email, and Developer contact email
   - If External and in Testing mode, add yourself in **Test users**
4. Create credentials:
   - Go to **APIs & Services → Credentials → Create Credentials → OAuth client ID**
   - **Application type**: Desktop app
   - Download JSON and save as `credentials.json` in project root

### Step 2: Project Setup

1. Create a new project directory
2. Set up virtual environment:
   ```bash
   python -m venv venv
   source venv/bin/activate  # Linux/Mac
   .\venv\Scripts\Activate   # Windows
   ```
3. Install dependencies:
   ```bash
   pip install google-api-python-client google-auth-httplib2 google-auth-oauthlib pycryptodome tqdm
   ```

## 📁 Project Structure

```
Project_Name/
├── credentials.json          # OAuth client (from Google Cloud Console)
├── token.pickle              # Generated after first OAuth success
├── auth.py                   # Authentication helper
├── crypto_utils.py           # AES encryption/decryption helpers
├── main.py                   # Main orchestration script
└── (venv)/                   # Virtual environment
```

## 📝 Code Files

### auth.py - Authentication Helper

```python
import os
import pickle
from googleapiclient.discovery import build
from google_auth_oauthlib.flow import InstalledAppFlow
from google.auth.transport.requests import Request

SCOPES = ['https://www.googleapis.com/auth/drive.file']

def get_gdrive_service():
    creds = None
    if os.path.exists('token.pickle'):
        with open('token.pickle', 'rb') as token:
            creds = pickle.load(token)
    if not creds or not creds.valid:
        if creds and creds.expired and creds.refresh_token:
            creds.refresh(Request())
        else:
            flow = InstalledAppFlow.from_client_secrets_file('credentials.json', SCOPES)
            creds = flow.run_local_server(port=0)
        with open('token.pickle', 'wb') as token:
            pickle.dump(creds, token)
    return build('drive', 'v3', credentials=creds)
```

### crypto_utils.py - Encryption/Decryption

```python
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes

def encrypt_file(input_file, output_file, key):
    cipher = AES.new(key, AES.MODE_EAX)
    with open(input_file, 'rb') as f:
        data = f.read()
    ciphertext, tag = cipher.encrypt_and_digest(data)
    with open(output_file, 'wb') as f:
        for x in (cipher.nonce, tag, ciphertext):
            f.write(x)

def decrypt_file(input_file, output_file, key):
    with open(input_file, 'rb') as f:
        nonce, tag, ciphertext = [f.read(x) for x in (16, 16, -1)]
    cipher = AES.new(key, AES.MODE_EAX, nonce)
    data = cipher.decrypt_and_verify(ciphertext, tag)
    with open(output_file, 'wb') as f:
        f.write(data)

def generate_key():
    return get_random_bytes(16)  # 128-bit AES key
```

### main.py - Main Application

```python
from auth import get_gdrive_service
from crypto_utils import encrypt_file, decrypt_file, generate_key
from googleapiclient.http import MediaFileUpload, MediaIoBaseDownload
import io

def upload_file(service, filepath, filename):
    file_metadata = { 'name': filename }
    media = MediaFileUpload(filepath, resumable=True)
    file = service.files().create(body=file_metadata, media_body=media, fields='id').execute()
    print("Uploaded file ID:", file.get('id'))
    return file.get('id')

def download_file(service, file_id, output_path):
    request = service.files().get_media(fileId=file_id)
    fh = io.FileIO(output_path, 'wb')
    downloader = MediaIoBaseDownload(fh, request)
    done = False
    while not done:
        status, done = downloader.next_chunk()
        print("Download %d%%." % int(status.progress() * 100))

if __name__ == "__main__":
    service = get_gdrive_service()
    key = generate_key()
    print("Generated AES Key:", key)

    # Create sample file
    with open("mysecret.txt", "w", encoding="utf-8") as f:
        f.write("This is a confidential test file.")

    # Encrypt → Upload → Download → Decrypt
    encrypt_file("mysecret.txt", "mysecret.enc", key)
    print("File encrypted.")
    file_id = upload_file(service, "mysecret.enc", "mysecret.enc")
    download_file(service, file_id, "mysecret_downloaded.enc")
    decrypt_file("mysecret_downloaded.enc", "mysecret_decrypted.txt", key)
    print("Decryption complete. Check mysecret_decrypted.txt")
```

## ▶️ Running the Project

1. Place `credentials.json` in your project root
2. Run `main.py`
3. Browser opens for Google sign-in → Allow requested permissions
4. First run creates `token.pickle`
5. Expected console output:
   ```
   Generated AES Key: b'...'
   File encrypted.
   Uploaded file ID: 1AbCDEfgHiJKlmnOPQ...
   Download 100%.
   Decryption complete. Check mysecret_decrypted.txt
   ```

## ✅ Validation & Testing Checklist

- [ ] Compare files: `fc mysecret.txt mysecret_decrypted.txt` (Windows)
- [ ] Open `mysecret.enc` in hex viewer — should look random (no readable text)
- [ ] Try different file types: `.png`, `.pdf`, `.docx`, `.zip`
- [ ] Try larger files and observe progress logs

## 🔒 Security Considerations

| Aspect | Recommendation |
|--------|----------------|
| **OAuth Scope** | `drive.file` - grants access only to files created/opened by app |
| **Tokens** | `token.pickle` stores OAuth tokens locally; delete to force re-consent |
| **AES Keys** | Store securely - use environment variables, secrets manager, or cloud KMS |

## 🔧 Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| `HttpError 403 accessNotConfigured` | Drive API not enabled | Enable Drive API; replace `credentials.json`; delete `token.pickle` |
| `redirect_uri_mismatch` | Wrong OAuth client type | Ensure OAuth client is Desktop app; re-download credentials |
| "This app isn't verified" | External app in Testing mode | Add yourself as Test user; proceed with warning |
| Upload fails intermittently | Network issues or expired token | Retry; delete `token.pickle`; check internet |
| `ValueError: MAC check failed` | Wrong AES key or corrupted download | Use exact encryption key; re-download file |

## 📚 Quick Commands Reference

```bash
# Install dependencies
pip install google-api-python-client google-auth-httplib2 google-auth-oauthlib pycryptodome tqdm

# Force re-authentication
del token.pickle  # Windows
rm token.pickle   # Linux/Mac
```
