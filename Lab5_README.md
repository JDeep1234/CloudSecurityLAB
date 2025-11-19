# Lab 5 — Heroku SQL Injection Demo

README documenting a vulnerable and secure SQL injection demo app deployed on Heroku.

## 🚀 Overview
This project demonstrates:
- A **vulnerable login endpoint** (string‑concatenated SQL)
- A **secure login endpoint** (parameterized queries / prepared statements)
- Heroku deployment with Postgres
- SQL injection testing (manual + sqlmap)

## 📁 Project Structure
```
heroku-sql-demo/
├── server.js
├── package.json
├── .env
└── public/
    ├── login-vulnerable.html
    ├── login-secure.html
    └── style.css
```

## 🛠️ Setup (Local)
```bash
npm install
node server.js
```

## 🌐 Deploying to Heroku
```bash
heroku create my-sql-injection-demo
heroku addons:create heroku-postgresql:hobby-dev
git push heroku main
```

## 🧪 SQL Injection Demo
Try on **vulnerable login**:
```
' OR '1'='1
```

## 🔐 Secure Endpoint
Uses:
- Prepared statements
- Input validation
- Rate limiting
- Password hashing (bcrypt)

## 📝 License
MIT
