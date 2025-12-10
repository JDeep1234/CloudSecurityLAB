# Lab 5: SQL Injection Prevention & Data Leak Security

## 📋 Overview

Build and deploy a **Node.js application on Heroku** that demonstrates **SQL Injection vulnerabilities** and their prevention using **parameterized queries**. This lab provides both vulnerable and secure login endpoints to understand the risks and proper mitigation techniques.

## 🎯 Learning Objectives

- Understand SQL Injection attack vectors
- Implement vulnerable vs. secure database queries
- Deploy a Node.js application to Heroku with PostgreSQL
- Test SQL injection using manual methods and SQLMap
- Implement parameterized queries for SQL injection prevention

## 🛠️ Prerequisites & Requirements

| Component | Requirement | Notes |
|-----------|-------------|-------|
| Node.js | LTS version | npm/yarn included |
| Git | Latest version | For version control |
| Heroku CLI | Latest version | [Download here](https://devcenter.heroku.com/articles/heroku-cli) |
| PostgreSQL | Local installation | For development |
| SQLMap | pip install sqlmap | **Only for testing your own app** |
| Tools | VS Code, Postman/curl | For development and testing |

## 🚀 Setup Instructions

### Step 1: Create Project & Initialize

```bash
mkdir heroku-sql-demo
cd heroku-sql-demo
git init
npm init -y
```

### Step 2: Install Dependencies

```bash
npm i express pg bcryptjs dotenv helmet express-rate-limit express-validator

# Optional for development
npm i -D nodemon
```

### Step 3: Heroku Setup

```bash
# Login to Heroku
heroku login

# Create Heroku app
heroku create my-sql-injection-demo-<your-unique-suffix>

# Add PostgreSQL addon
heroku addons:create heroku-postgresql:hobby-dev

# Check config (should show DATABASE_URL)
heroku config --app my-sql-injection-demo-<suffix>
```

### Step 4: Configure Git Identity (if needed)

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

## 📁 Project Structure

```
heroku-sql-demo/
├── server.js
├── package.json
└── public/
    ├── login-vulnerable.html
    ├── login-secure.html
    └── style.css
```

## 📝 Code Files

### server.js - Main Application

```javascript
const express = require('express');
const { Pool } = require('pg');
const path = require('path');

const app = express();
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Serve static files from /public
app.use(express.static(path.join(__dirname, 'public')));

// Connect to Postgres
const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
  ssl: { rejectUnauthorized: false },
});

// --- Home Page ---
app.get('/', (req, res) => {
  res.send(`
    <h2>SQL Injection Demo</h2>
    <p>⚠️ Try <a href="/login-vulnerable.html">/login-vulnerable</a> to see SQL Injection in action (unsafe)</p>
    <p>✅ Try <a href="/login-secure.html">/login-secure</a> to see the safe version (parameterized)</p>
  `);
});

// --- Vulnerable handler (uses string concatenation ❌) ---
app.post('/login-vulnerable', async (req, res) => {
  const { username, password } = req.body;
  try {
    // ⚠️ VULNERABLE: String concatenation allows SQL injection
    const query = `SELECT * FROM users WHERE username='${username}' AND password='${password}'`;
    console.log("Executing query:", query);

    const result = await pool.query(query);

    if (result.rows.length > 0) {
      res.send('⚠️ Login successful (but vulnerable to SQL Injection)!');
    } else {
      res.status(401).send('❌ Unauthorized');
    }
  } catch (err) {
    console.error(err);
    res.status(500).send('⚠️ Server error');
  }
});

// --- Secure handler (parameterized ✅) ---
app.post('/login-secure', async (req, res) => {
  const { username, password } = req.body;
  try {
    // ✅ SECURE: Parameterized query prevents SQL injection
    const result = await pool.query(
      'SELECT * FROM users WHERE username=$1 AND password=$2',
      [username, password]
    );
    if (result.rows.length > 0) {
      res.send('✅ Login successful (safe from SQL Injection)!');
    } else {
      res.status(401).send('❌ Unauthorized');
    }
  } catch (err) {
    console.error(err);
    res.status(500).send('⚠️ Server error');
  }
});

app.listen(process.env.PORT || 5000, () => console.log('🚀 Running'));
```

### public/login-vulnerable.html

```html
<!DOCTYPE html>
<html>
<head>
  <title>Vulnerable Login</title>
</head>
<body>
  <h2>⚠️ Vulnerable Login (Unsafe Example)</h2>
  <form action="/login-vulnerable" method="POST">
    <input type="text" name="username" placeholder="Username" required /><br/><br/>
    <input type="password" name="password" placeholder="Password" required /><br/><br/>
    <button type="submit">Login</button>
  </form>

  <p style="color:red;">
    This form is vulnerable to SQL Injection.<br>
    Try: <b>' OR '1'='1</b> in the username or password field.
  </p>

  <p><a href="/">⬅ Back to Home</a></p>
</body>
</html>
```

### public/login-secure.html

```html
<!DOCTYPE html>
<html>
<head>
  <title>Secure Login</title>
</head>
<body>
  <h2>✅ Secure Login (Parameterized Queries)</h2>
  <form action="/login-secure" method="POST">
    <input type="text" name="username" placeholder="Username" required /><br/><br/>
    <input type="password" name="password" placeholder="Password" required /><br/><br/>
    <button type="submit">Login</button>
  </form>

  <p style="color:green;">
    This form is safe because it uses parameterized queries.<br>
    SQL Injection attempts will fail.
  </p>

  <p><a href="/">⬅ Back to Home</a></p>
</body>
</html>
```

## 🗄️ Database Setup

### Initialize PostgreSQL in Heroku

```bash
heroku pg:psql --app <my-sql-injection-demo-name>
```

Inside the psql prompt:

```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(100),
    password VARCHAR(100)
);

INSERT INTO users (username, password) VALUES
('admin', 'admin123'),
('test', 'test123');

-- Exit psql
\q
```

## 🚀 Deploy Application

```bash
git add .
git commit -m "Deploy SQL injection demo"
git push heroku main
# (If your branch is master, use: git push heroku master)

# Scale dynos
heroku ps:scale web=1 --app my-sql-injection-demo-name

# Open app
heroku open --app my-sql-injection-demo-name
```

## 🧪 Testing SQL Injection

### Test Case 1: Normal Login (Valid User)

1. Go to `/login-vulnerable.html`
2. Enter: `admin` / `admin123`
3. **Expected**: `⚠️ Login successful (but vulnerable to SQL Injection)!`

### Test Case 2: Wrong Password

1. Enter: `admin` / `wrongpassword`
2. **Expected**: `❌ Unauthorized`

### Test Case 3: SQL Injection Attack ⚠️

1. Go to `/login-vulnerable.html`
2. Enter:
   - **Username**: `' OR '1'='1`
   - **Password**: `anything` (e.g., `abc`)
3. **What happens**: The query becomes:
   ```sql
   SELECT * FROM users WHERE username='' OR '1'='1' AND password='abc';
   ```
   Since `'1'='1'` is always true, Postgres returns all rows.
4. **Expected**: `⚠️ Login successful (but vulnerable to SQL Injection)!`

### Test Case 4: Secure Page Protection

1. Go to `/login-secure.html`
2. Try the same injection:
   - **Username**: `' OR '1'='1`
   - **Password**: `abc`
3. The query uses parameters:
   ```sql
   SELECT * FROM users WHERE username=$1 AND password=$2;
   ```
   This looks for a literal username of `' OR '1'='1` (not evaluated as SQL).
4. **Expected**: `❌ Unauthorized`

### Using SQLMap for Testing

```bash
# Install SQLMap
pip install sqlmap

# Test your app (replace with your app URL)
sqlmap -u "https://my-sql-injection-demo-name.herokuapp.com/login-secure" \
  --data="username=admin&password=wrong" --risk=3 --level=5
```

If your query is secure, SQLMap will report: **"parameter not injectable"**

## 📊 Monitor Logs

While testing, keep logs open:

```bash
heroku logs --tail --app my-sql-injection-demo-name
```

## ✅ Validation & Testing Checklist

- [ ] Normal login works with valid credentials
- [ ] Wrong credentials return "Unauthorized"
- [ ] SQL injection succeeds on vulnerable endpoint
- [ ] SQL injection fails on secure endpoint
- [ ] SQLMap reports secure endpoint as not injectable
- [ ] Logs show query execution

## 🔒 Security Comparison

| Aspect | Vulnerable | Secure |
|--------|------------|--------|
| **Query Type** | String concatenation | Parameterized query |
| **SQL Injection** | ✅ Possible | ❌ Blocked |
| **User Input** | Directly in SQL | Escaped as parameter |
| **Attack Vector** | `' OR '1'='1` bypasses auth | Input treated as literal string |

## 🔧 Troubleshooting

| Issue | Solution |
|-------|----------|
| Database connection error | Check `DATABASE_URL` in Heroku config |
| App not starting | Check `heroku logs --tail` for errors |
| Git push rejected | Ensure `git add .` and `git commit` before push |
| SQLMap not found | Run `pip install sqlmap` |

## 📚 Key Takeaways

### ❌ Vulnerable Code (Never Do This)

```javascript
const query = `SELECT * FROM users WHERE username='${username}' AND password='${password}'`;
```

### ✅ Secure Code (Always Do This)

```javascript
const result = await pool.query(
  'SELECT * FROM users WHERE username=$1 AND password=$2',
  [username, password]
);
```

## 🛡️ Additional Security Measures

1. **Input Validation**: Use `express-validator` to sanitize inputs
2. **Rate Limiting**: Use `express-rate-limit` to prevent brute force
3. **Password Hashing**: Use `bcryptjs` to hash passwords
4. **HTTPS**: Always use SSL/TLS in production
5. **Security Headers**: Use `helmet` middleware
