# Lab 2: Firebase Multi-Factor Authentication (MFA)

## College Lab Write-Up

---

### **Experiment No:** 2

### **Title:** Implementation of Multi-Factor Authentication using Firebase in React Application

---

### **Aim:**
To develop a React web application that implements Multi-Factor Authentication (MFA) using Firebase Authentication with email/password and SMS-based verification.

---

### **Objectives:**
1. Understand the concept and importance of Multi-Factor Authentication
2. Configure Firebase Authentication with email and phone providers
3. Implement email verification workflow
4. Implement SMS-based second factor enrollment and verification
5. Handle MFA challenge during user sign-in

---

### **Theory:**

**Multi-Factor Authentication (MFA):**
MFA is a security mechanism that requires users to provide two or more verification factors to gain access. The factors typically include:
- **Something you know** (password)
- **Something you have** (phone/token)
- **Something you are** (biometrics)

**Firebase Authentication:**
Firebase Authentication provides backend services and SDKs for authenticating users. It supports various authentication methods including email/password, phone, Google, Facebook, and more. Firebase also supports Multi-Factor Authentication with phone as a second factor.

**SMS-based OTP:**
One-Time Password (OTP) sent via SMS is a common second factor. The server generates a random code, sends it to the user's phone, and verifies the code entered by the user.

**reCAPTCHA:**
reCAPTCHA is a service that protects against spam and abuse. Firebase uses reCAPTCHA to verify that phone authentication requests come from legitimate users, not bots.

---

### **Software/Hardware Requirements:**

| Component | Specification |
|-----------|---------------|
| Operating System | Windows 10/11, Linux, or macOS |
| Node.js | Version 14 or higher |
| npm | Comes with Node.js |
| IDE | VS Code or any JavaScript IDE |
| Browser | Chrome, Firefox, or Edge |
| Firebase Account | Google account for Firebase Console |
| Phone Number | For SMS verification testing |

---

### **Algorithm/Procedure:**

**Step 1: Firebase Console Setup**
1. Create new Firebase project
2. Register web application
3. Enable Email/Password authentication provider
4. Enable Phone authentication provider
5. Enable Multi-Factor Authentication
6. Add localhost to authorized domains
7. Add test phone numbers (for development)

**Step 2: React Project Setup**
1. Create React app using create-react-app
2. Install Firebase SDK
3. Configure Firebase with project credentials

**Step 3: User Registration Flow**
1. User enters email and password
2. Create user with createUserWithEmailAndPassword()
3. Send email verification link
4. User verifies email before MFA enrollment

**Step 4: MFA Enrollment Flow**
1. User logs in with verified email
2. User enters phone number for MFA
3. Initialize reCAPTCHA verifier
4. Get MFA session from current user
5. Send OTP to phone number
6. User enters OTP
7. Create phone credential and assertion
8. Enroll second factor

**Step 5: MFA Sign-in Flow**
1. User attempts sign-in with email/password
2. Firebase triggers MFA challenge
3. User receives OTP on enrolled phone
4. User enters OTP
5. Verify and complete sign-in

---

### **Program/Code:**

*(Refer to the detailed implementation in [README_Lab2_Firebase_MFA.md](./README_Lab2_Firebase_MFA.md))*

**Key Code Snippets:**

```javascript
// Firebase Configuration
import { initializeApp } from "firebase/app";
import { getAuth } from "firebase/auth";

const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT.firebaseapp.com",
  projectId: "YOUR_PROJECT"
};

const app = initializeApp(firebaseConfig);
export const auth = getAuth(app);
```

```javascript
// MFA Enrollment - Send OTP
const sendOtp = async () => {
  const recaptchaVerifier = new RecaptchaVerifier(
    getAuth(), "recaptcha-container", {}
  );
  const mfaSession = await multiFactor(auth.currentUser).getSession();
  const phoneAuthProvider = new PhoneAuthProvider(auth);
  const verificationId = await phoneAuthProvider.verifyPhoneNumber(
    { phoneNumber: phone, session: mfaSession },
    recaptchaVerifier
  );
};

// MFA Enrollment - Verify OTP
const verifyOtp = async () => {
  const cred = PhoneAuthProvider.credential(verificationId, otp);
  const assertion = PhoneMultiFactorGenerator.assertion(cred);
  await multiFactor(auth.currentUser).enroll(assertion, "Phone");
};
```

---

### **Expected Output:**

1. **Sign Up:** "✅ Signed up! Verify your email before MFA."
2. **Email Verified:** Email verification link confirmed
3. **OTP Sent:** "📲 OTP sent!"
4. **MFA Enrolled:** "✅ MFA Enrolled!"
5. **Re-login:** MFA challenge triggered, OTP required
6. **MFA Success:** User logged in with two-factor authentication

---

### **Result:**
The React application successfully implemented:
1. User registration with email/password
2. Email verification before MFA enrollment
3. Phone number enrollment as second factor
4. OTP generation and verification via SMS
5. MFA challenge during subsequent logins
6. Complete two-factor authentication workflow

---

### **Conclusion:**
This experiment demonstrated the implementation of Multi-Factor Authentication using Firebase in a React application. The key learnings include:
- MFA significantly enhances security by requiring multiple verification factors
- Firebase provides a comprehensive authentication solution with built-in MFA support
- Email verification should precede MFA enrollment to ensure valid user accounts
- reCAPTCHA integration prevents automated abuse of phone verification
- The combination of password and SMS OTP provides a balance of security and usability

---

### **Viva Questions:**

1. **What is Multi-Factor Authentication?**
   - MFA requires users to provide two or more verification factors (something you know, have, or are) to access an account.

2. **Why is email verification required before MFA enrollment?**
   - To ensure the email address belongs to the user and prevent fake account creation.

3. **What is the role of reCAPTCHA in phone authentication?**
   - reCAPTCHA verifies that requests come from humans, not bots, preventing abuse of SMS services.

4. **What are the three factors in MFA?**
   - Knowledge (password), Possession (phone/token), and Inherence (biometrics).

5. **Why is SMS-based MFA considered less secure than app-based authenticators?**
   - SMS can be intercepted through SIM swapping attacks, while authenticator apps generate codes locally.

6. **What is Firebase Authentication?**
   - Firebase Authentication is a service that provides backend infrastructure for authenticating users using various methods.

---

### **References:**
1. Firebase Authentication Documentation: https://firebase.google.com/docs/auth
2. Firebase MFA Documentation: https://firebase.google.com/docs/auth/web/multi-factor
3. React Documentation: https://reactjs.org/docs
4. OWASP MFA Guidelines: https://owasp.org/www-community/Multi-Factor_Authentication
