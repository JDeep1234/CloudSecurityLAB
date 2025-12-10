# Lab 5: SQL Injection Prevention & Data Leak Security

## College Lab Write-Up

---

### **Experiment No:** 5

### **Title:** SQL Injection Attack Demonstration and Prevention using Parameterized Queries

---

### **Aim:**
To understand SQL Injection vulnerabilities, demonstrate attack scenarios, and implement secure coding practices using parameterized queries in a Node.js application deployed on Heroku.

---

### **Objectives:**
1. Understand SQL Injection attack vectors and their impact
2. Implement a vulnerable login endpoint to demonstrate the attack
3. Implement a secure login endpoint using parameterized queries
4. Deploy the application on Heroku cloud platform
5. Test and validate both vulnerable and secure implementations
6. Learn secure coding practices for database interactions

---

### **Theory:**

**SQL Injection:**
SQL Injection is a code injection technique that exploits security vulnerabilities in an application's database layer. It occurs when user input is incorrectly filtered or not strongly typed, allowing attackers to execute arbitrary SQL commands.

**Types of SQL Injection:**
1. **In-band SQLi:** Attacker uses same channel for attack and results
   - Error-based: Uses database error messages
   - Union-based: Uses UNION SQL operator
2. **Blind SQLi:** No direct output, inferred from application behavior
   - Boolean-based: Uses true/false conditions
   - Time-based: Uses time delays
3. **Out-of-band SQLi:** Uses different channels for attack and results

**SQL Injection Attack Example:**
```sql
-- Original Query
SELECT * FROM users WHERE username='admin' AND password='password123';

-- Injected Query (with input: ' OR '1'='1)
SELECT * FROM users WHERE username='' OR '1'='1' AND password='anything';
-- '1'='1' is always true, bypassing authentication
```

**Parameterized Queries:**
Parameterized queries (prepared statements) separate SQL code from data. User input is treated as data, not executable code, preventing injection attacks.

**Heroku:**
Heroku is a cloud Platform as a Service (PaaS) supporting multiple programming languages. It provides easy deployment, managed PostgreSQL, and automatic scaling.

---

### **Software/Hardware Requirements:**

| Component | Specification |
|-----------|---------------|
| Operating System | Windows 10/11, Linux, or macOS |
| Node.js | LTS version |
| npm | Package manager (with Node.js) |
| Git | Version control |
| Heroku CLI | For deployment |
| PostgreSQL | Database (Heroku addon) |
| SQLMap | For testing (optional) |
| IDE | VS Code or similar |

---

### **Algorithm/Procedure:**

**Step 1: Project Setup**
1. Create project directory
2. Initialize npm project
3. Install dependencies (express, pg)
4. Initialize Git repository

**Step 2: Heroku Setup**
1. Login to Heroku CLI
2. Create Heroku application
3. Add PostgreSQL addon
4. Note DATABASE_URL from config

**Step 3: Database Setup**
1. Connect to Heroku PostgreSQL
2. Create users table
3. Insert test user data

**Step 4: Implement Vulnerable Endpoint**
1. Create /login-vulnerable route
2. Use string concatenation for SQL query
3. Execute query with user input directly

**Step 5: Implement Secure Endpoint**
1. Create /login-secure route
2. Use parameterized query ($1, $2 placeholders)
3. Pass user input as parameters array

**Step 6: Create Frontend**
1. Create login-vulnerable.html
2. Create login-secure.html
3. Add forms pointing to respective endpoints

**Step 7: Deploy Application**
1. Add files to Git
2. Commit changes
3. Push to Heroku
4. Scale web dyno

**Step 8: Testing**
1. Test normal login (valid credentials)
2. Test invalid login (wrong password)
3. Test SQL injection on vulnerable endpoint
4. Test SQL injection on secure endpoint
5. Verify secure endpoint blocks attack

---

### **Program/Code:**

*(Refer to the detailed implementation in [README_Lab5_SQL_Injection.md](./README_Lab5_SQL_Injection.md))*

**Key Code Snippets:**

```javascript
// VULNERABLE - String Concatenation (NEVER DO THIS)
app.post('/login-vulnerable', async (req, res) => {
  const { username, password } = req.body;
  
  // ❌ Dangerous: Direct string concatenation
  const query = `SELECT * FROM users 
    WHERE username='${username}' AND password='${password}'`;
  
  const result = await pool.query(query);
  // Attacker can inject: ' OR '1'='1
});
```

```javascript
// SECURE - Parameterized Query (ALWAYS DO THIS)
app.post('/login-secure', async (req, res) => {
  const { username, password } = req.body;
  
  // ✅ Safe: Parameterized query
  const result = await pool.query(
    'SELECT * FROM users WHERE username=$1 AND password=$2',
    [username, password]  // Parameters passed separately
  );
  // User input treated as data, not code
});
```

```sql
-- Database Setup
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(100),
    password VARCHAR(100)
);

INSERT INTO users (username, password) VALUES
('admin', 'admin123'),
('test', 'test123');
```

---

### **Expected Output:**

**Test Case 1: Normal Login**
- Input: username=`admin`, password=`admin123`
- Output: `⚠️ Login successful (but vulnerable to SQL Injection)!`

**Test Case 2: Wrong Password**
- Input: username=`admin`, password=`wrongpass`
- Output: `❌ Unauthorized`

**Test Case 3: SQL Injection on Vulnerable Endpoint**
- Input: username=`' OR '1'='1`, password=`anything`
- Generated Query: `SELECT * FROM users WHERE username='' OR '1'='1' AND password='anything'`
- Output: `⚠️ Login successful (but vulnerable to SQL Injection)!`
- **Attack Successful!**

**Test Case 4: SQL Injection on Secure Endpoint**
- Input: username=`' OR '1'='1`, password=`anything`
- Query treats input as literal string: `' OR '1'='1`
- Output: `❌ Unauthorized`
- **Attack Blocked!**

---

### **Result:**
The experiment successfully demonstrated:
1. SQL Injection vulnerability through string concatenation
2. Attack bypass of authentication using `' OR '1'='1`
3. Prevention of SQL Injection using parameterized queries
4. Deployment of secure application on Heroku cloud
5. Clear comparison between vulnerable and secure implementations

---

### **Conclusion:**
This experiment illustrated the dangers of SQL Injection attacks and the importance of secure coding practices. Key takeaways include:
- SQL Injection remains one of the most critical web vulnerabilities (OWASP Top 10)
- String concatenation in SQL queries is inherently dangerous
- Parameterized queries effectively prevent SQL Injection by separating code from data
- Input validation alone is insufficient; parameterized queries are mandatory
- Security testing should be part of the development lifecycle
- Cloud deployment platforms like Heroku make it easy to test applications

---

### **Viva Questions:**

1. **What is SQL Injection?**
   - SQL Injection is an attack where malicious SQL code is inserted into application queries through user input, allowing unauthorized database access.

2. **What is the difference between string concatenation and parameterized queries?**
   - String concatenation directly embeds user input in SQL, allowing code injection. Parameterized queries treat user input as data, not executable code.

3. **What does `' OR '1'='1` do in SQL Injection?**
   - It creates a condition that's always true ('1'='1'), bypassing authentication logic.

4. **How do parameterized queries prevent SQL Injection?**
   - Parameters are escaped and treated as literal values, not SQL commands, preventing malicious code execution.

5. **What is OWASP Top 10?**
   - OWASP Top 10 is a list of the most critical web application security risks, with Injection attacks consistently ranking high.

6. **What other measures can prevent SQL Injection?**
   - Input validation, stored procedures, least privilege principle, Web Application Firewalls (WAF), and ORM frameworks.

7. **What is SQLMap?**
   - SQLMap is an open-source tool that automates detection and exploitation of SQL Injection vulnerabilities.

---

### **References:**
1. OWASP SQL Injection: https://owasp.org/www-community/attacks/SQL_Injection
2. Node.js pg Documentation: https://node-postgres.com/
3. Heroku Documentation: https://devcenter.heroku.com/
4. SQLMap: https://sqlmap.org/
5. OWASP Top 10: https://owasp.org/www-project-top-ten/
