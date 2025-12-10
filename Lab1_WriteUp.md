# Lab 1: Encrypted Cloud File Storage System

## College Lab Write-Up

---

### **Experiment No:** 1

### **Title:** Encrypted Cloud File Storage System using Google Drive and AES Encryption

---

### **Aim:**
To develop a Python application that encrypts files locally using AES encryption and securely uploads them to Google Drive using OAuth 2.0 authentication.

---

### **Objectives:**
1. Understand client-side encryption before cloud storage
2. Implement AES-128 encryption using PyCryptodome library
3. Integrate Google Drive API with OAuth 2.0 authentication
4. Demonstrate secure file upload, download, and decryption workflow

---

### **Theory:**

**Cloud Storage Security:**
Cloud storage services like Google Drive store data on remote servers. While convenient, this raises security concerns as data may be accessed by service providers or attackers. Client-side encryption addresses this by encrypting data before upload.

**AES (Advanced Encryption Standard):**
AES is a symmetric block cipher adopted by the U.S. government. It operates on 128-bit blocks and supports key sizes of 128, 192, or 256 bits. In this lab, we use AES-128 in EAX mode, which provides both encryption and authentication.

**OAuth 2.0:**
OAuth 2.0 is an authorization framework that enables applications to obtain limited access to user accounts. It works by delegating user authentication to the service hosting the user account and authorizing third-party applications to access the user account.

**Google Drive API:**
The Google Drive API allows applications to interact with Google Drive storage. It supports operations like file upload, download, listing, and management through RESTful endpoints.

---

### **Software/Hardware Requirements:**

| Component | Specification |
|-----------|---------------|
| Operating System | Windows 10/11, Linux, or macOS |
| Python | Version 3.7 or higher |
| IDE | PyCharm, VS Code, or any Python IDE |
| Libraries | google-api-python-client, google-auth-oauthlib, pycryptodome |
| Internet | Required for Google Drive API access |
| Google Account | For OAuth credentials and Drive storage |

---

### **Algorithm/Procedure:**

**Step 1: Setup Google Cloud Project**
1. Create project in Google Cloud Console
2. Enable Google Drive API
3. Configure OAuth consent screen
4. Create OAuth 2.0 credentials (Desktop App)
5. Download credentials.json

**Step 2: Setup Python Environment**
1. Create virtual environment
2. Install required libraries
3. Place credentials.json in project directory

**Step 3: Authentication (auth.py)**
1. Check for existing token.pickle
2. If valid token exists, use it
3. Otherwise, initiate OAuth flow
4. Save new token for future use
5. Build and return Drive service object

**Step 4: Encryption (crypto_utils.py)**
1. Generate random 128-bit AES key
2. Create AES cipher in EAX mode
3. Encrypt file data
4. Write nonce, tag, and ciphertext to output file

**Step 5: Main Workflow (main.py)**
1. Authenticate with Google Drive
2. Generate AES encryption key
3. Create sample file with sensitive data
4. Encrypt file locally
5. Upload encrypted file to Drive
6. Download encrypted file from Drive
7. Decrypt file using same key
8. Verify decrypted content matches original

---

### **Program/Code:**

*(Refer to the detailed implementation in [README_Lab1_GDrive_Encrypt.md](./README_Lab1_GDrive_Encrypt.md))*

**Key Code Snippets:**

```python
# AES Encryption
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
```

```python
# Google Drive Upload
def upload_file(service, filepath, filename):
    file_metadata = {'name': filename}
    media = MediaFileUpload(filepath, resumable=True)
    file = service.files().create(
        body=file_metadata, 
        media_body=media, 
        fields='id'
    ).execute()
    return file.get('id')
```

---

### **Expected Output:**

```
Generated AES Key: b'\x8f\x12\xa3...'
File encrypted.
Uploaded file ID: 1AbCDEfgHiJKlmnOPQ...
Download 100%.
Decryption complete. Check mysecret_decrypted.txt
```

---

### **Result:**
The Python application successfully:
1. Encrypted a text file using AES-128 encryption
2. Uploaded the encrypted file to Google Drive via OAuth 2.0
3. Downloaded the encrypted file from Google Drive
4. Decrypted the file to recover original content
5. Verified that original and decrypted files are identical

---

### **Conclusion:**
This experiment demonstrated the implementation of a secure cloud file storage system using client-side AES encryption combined with Google Drive API. The approach ensures that:
- Files are encrypted before leaving the local machine
- Only users with the correct AES key can decrypt the data
- OAuth 2.0 provides secure, token-based authentication
- The principle of "zero-knowledge" encryption is achieved where the cloud provider cannot read the stored data

---

### **Viva Questions:**

1. **What is AES and why is it used for encryption?**
   - AES (Advanced Encryption Standard) is a symmetric encryption algorithm. It's used because it's fast, secure, and widely adopted as a standard.

2. **What is the difference between symmetric and asymmetric encryption?**
   - Symmetric uses the same key for encryption and decryption. Asymmetric uses a public-private key pair.

3. **What is OAuth 2.0?**
   - OAuth 2.0 is an authorization framework that allows third-party applications to access user data without exposing passwords.

4. **Why do we encrypt files before uploading to cloud?**
   - To ensure data privacy and security, preventing unauthorized access even if the cloud provider is compromised.

5. **What is EAX mode in AES?**
   - EAX is an authenticated encryption mode that provides both confidentiality and integrity verification.

---

### **References:**
1. Google Drive API Documentation: https://developers.google.com/drive/api
2. PyCryptodome Documentation: https://pycryptodome.readthedocs.io
3. OAuth 2.0 Specification: https://oauth.net/2/
