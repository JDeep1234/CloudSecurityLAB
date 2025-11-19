# Lab 2 — Firebase MFA (React App)

README for a React + Firebase MFA authentication demo.

## 🔐 Features
- Email/password signup  
- Email verification  
- MFA via SMS (phone as second factor)  
- Secure login challenge flow  

## 🛠 Technologies
- React (CRA)
- Firebase Auth (Modular SDK)
- reCAPTCHA v3 for SMS verification

## ⚙️ Setup
```bash
npm install firebase
npm start
```

## 📱 MFA Flow
1. Signup → verify email  
2. Login → enroll phone → receive OTP  
3. Re-login → SMS challenge → OTP verification  

## 📝 License
MIT
