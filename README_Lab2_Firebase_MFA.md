# Lab 2: Firebase Multi-Factor Authentication (MFA) — React Setup

## 📋 Overview

A React application with **Firebase Authentication** that supports email/password login, email verification, and **SMS-based Multi-Factor Authentication (MFA)**. Users enroll a phone number as a second factor and are challenged during sign-in.

## 🎯 Learning Objectives

- Implement Firebase Authentication in a React application
- Configure email/password authentication with email verification
- Set up SMS-based Multi-Factor Authentication (MFA)
- Handle MFA enrollment and challenge flows
- Implement reCAPTCHA verification for phone authentication

## 🛠️ Prerequisites & Requirements

| Component | Minimum Requirement | Notes |
|-----------|---------------------|-------|
| Node.js + npm | Node 14+ | Check with `node -v`, `npm -v` |
| IDE | VSCode | Any IDE that supports React/JS |
| Firebase Project | Google Firebase | Enable Authentication services |
| Firebase SDK | Modular v9 or v10 | Installed via npm |
| Authorized domains | localhost, 127.0.0.1 | Must be added in Firebase console |

## 🚀 Setup Instructions

### Step 1: Firebase Console Setup

1. Go to [Firebase Console](https://console.firebase.google.com) → Create a new project
2. Register a **Web App** (`</>`) and copy the configuration snippet
3. Enable **Authentication → Providers**:
   - ✅ Email/Password
   - ✅ Phone
4. Enable **Multi-Factor Authentication (SMS)**
5. Add **Authorized domains**: `localhost`, `127.0.0.1`
6. Under **Phone Authentication**, add test numbers for development

### Step 2: Create React Project

```bash
npx create-react-app mfa-demo-react
cd mfa-demo-react
npm install firebase
npm start
```

Open the app at: http://localhost:3000

## 📁 Project Structure

```
mfa-demo-react/
├── package.json
└── src/
    ├── index.js
    ├── firebase.js
    └── App.js
```

## 📝 Code Files

### firebase.js - Firebase Configuration

```javascript
import { initializeApp } from "firebase/app";
import { getAuth } from "firebase/auth";

const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT",
  storageBucket: "YOUR_PROJECT.appspot.com",
  messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
  appId: "YOUR_APP_ID",
  measurementId: "YOUR_MEASUREMENT_ID"
};

const app = initializeApp(firebaseConfig);
export const auth = getAuth(app);
export default app;
```

### index.js - Entry Point

```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
```

### App.js - Core MFA Logic

```javascript
import React, { useState, useEffect } from "react";
import {
  createUserWithEmailAndPassword,
  signInWithEmailAndPassword,
  sendEmailVerification,
  signOut,
  onAuthStateChanged,
  multiFactor,
  PhoneAuthProvider,
  PhoneMultiFactorGenerator,
  RecaptchaVerifier,
  getAuth
} from "firebase/auth";
import { auth } from "./firebase";

function App() {
  const [user, setUser] = useState(null);
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [phone, setPhone] = useState("");
  const [otp, setOtp] = useState("");
  const [verificationId, setVerificationId] = useState("");

  useEffect(() => {
    const unsubscribe = onAuthStateChanged(auth, (u) => setUser(u));
    return () => unsubscribe();
  }, []);

  // Signup with email verification
  const handleSignup = async () => {
    try {
      const cred = await createUserWithEmailAndPassword(auth, email, password);
      await sendEmailVerification(cred.user);
      alert("✅ Signed up! Verify your email before MFA.");
    } catch (e) {
      alert(e.message);
    }
  };

  // Signin
  const handleSignin = async () => {
    try {
      await signInWithEmailAndPassword(auth, email, password);
      alert("✅ Signed in!");
    } catch (e) {
      alert(e.message);
    }
  };

  // Signout
  const handleSignout = () => signOut(auth);

  // Send OTP for MFA enrollment
  const sendOtp = async () => {
    try {
      const recaptchaVerifier = new RecaptchaVerifier(
        getAuth(),
        "recaptcha-container",
        {}
      );
      const mfaSession = await multiFactor(auth.currentUser).getSession();
      const phoneAuthProvider = new PhoneAuthProvider(auth);
      const vId = await phoneAuthProvider.verifyPhoneNumber(
        { phoneNumber: phone, session: mfaSession },
        recaptchaVerifier
      );
      setVerificationId(vId);
      alert("📲 OTP sent!");
    } catch (e) {
      alert("Send OTP failed: " + e.message);
    }
  };

  // Verify OTP and Enroll MFA
  const verifyOtp = async () => {
    try {
      const cred = PhoneAuthProvider.credential(verificationId, otp);
      const multiFactorAssertion = PhoneMultiFactorGenerator.assertion(cred);
      await multiFactor(auth.currentUser).enroll(multiFactorAssertion, "Personal Phone");
      alert("✅ MFA Enrolled!");
    } catch (e) {
      alert("Verify OTP failed: " + e.message);
    }
  };

  return (
    <div style={{ padding: "20px" }}>
      <h2>Firebase MFA Demo (React)</h2>

      {!user && (
        <div>
          <input placeholder="Email" onChange={(e) => setEmail(e.target.value)} /><br />
          <input type="password" placeholder="Password" onChange={(e) => setPassword(e.target.value)} /><br />
          <button onClick={handleSignup}>Sign Up</button>
          <button onClick={handleSignin}>Sign In</button>
        </div>
      )}

      {user && (
        <div>
          <p>Logged in as {user.email}</p>
          <button onClick={handleSignout}>Sign Out</button>

          {user.emailVerified && (
            <div>
              <h3>Enroll MFA</h3>
              <input placeholder="+91XXXXXXXXXX" onChange={(e) => setPhone(e.target.value)} /><br />
              <div id="recaptcha-container"></div>
              <button onClick={sendOtp}>Send OTP</button>
              <br />
              <input placeholder="Enter OTP" onChange={(e) => setOtp(e.target.value)} />
              <button onClick={verifyOtp}>Verify & Enroll</button>
            </div>
          )}
        </div>
      )}
    </div>
  );
}

export default App;
```

## ▶️ Running the Project

1. **Sign up** with email/password → check inbox → verify email
2. **Login** → MFA enrollment UI is shown
3. **Enter phone number**, send OTP, verify & enroll
4. **Sign out** → sign in again → MFA challenge (OTP) required
5. **Enter OTP** → MFA login success

## ✅ Validation & Testing Checklist

- [ ] Sign up, verify email, and enroll phone number
- [ ] On re-login, OTP challenge is triggered
- [ ] OTP verification allows access
- [ ] Test both success and failure flows

## 🔧 Troubleshooting

| Symptom | Cause | Fix |
|---------|-------|-----|
| OTP not delivered | Using real numbers in dev | Use Firebase test numbers |
| `appVerificationDisabledForTesting` error | Old v8 flag removed in v10 | Do not use `auth.settings.appVerificationDisabledForTesting` |
| reCAPTCHA not rendering | Missing div | Ensure `<div id="recaptcha-container"></div>` exists |
| Login blocked | Missing domain | Add `localhost` in Firebase Authorized domains |

## 🔒 Security Features Implemented

- **Email Verification**: Users must verify email before MFA enrollment
- **SMS-based MFA**: Second factor authentication via phone
- **reCAPTCHA**: Prevents automated abuse of phone verification
- **Session Management**: Proper authentication state handling

## 📚 Key Functions

| Function | Purpose |
|----------|---------|
| `handleSignup()` | Create user with email/password + send verification |
| `handleSignin()` | Sign in user (triggers MFA challenge if enrolled) |
| `sendOtp()` | Send OTP to phone for MFA enrollment |
| `verifyOtp()` | Verify OTP and complete MFA enrollment |
