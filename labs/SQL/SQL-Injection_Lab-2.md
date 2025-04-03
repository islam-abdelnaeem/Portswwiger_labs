# SQL Injection Lab Write-Up: Login Bypass

## Lab Overview

This write-up documents my solution to the **"SQL injection vulnerability allowing login bypass"** lab from PortSwigger's Web Security Academy. The goal of this lab is to exploit an SQL injection vulnerability in the login form to bypass authentication and log in as the "administrator" user without knowing the correct password.

## Objective

The objective is to manipulate the SQL query behind the login form to always return a valid result, allowing unauthorized access to the administrator account.

## Steps to Solve

### 1. Exploring the Login Form
- I started by examining the login page, which has two input fields: "username" and "password".
- Submitting random credentials (e.g., `test:test`) returned an "Invalid username or password" message, indicating the form queries a database to validate credentials.

### 2. Identifying the Vulnerable Parameter
- I suspected the username field might be vulnerable to SQL injection since it’s a common entry point for such attacks.
- To test this, I decided to inject SQL code into the username field while leaving the password field random.

### 3. Crafting the Payload
- I needed a payload that would make the SQL query return a valid user regardless of the password.
- I entered the following in the username field:
  ```sql
  administrator' OR '1'='1' --
  ```
- I left the password field as any random value (e.g., `123`).
- The full input was:
  - **Username:** `administrator' OR '1'='1' --`
  - **Password:** `123`

### 4. Understanding the Payload
- The payload `administrator' OR '1'='1' --` works by altering the SQL query:
  ```sql
  -- Original query (assumed)
  SELECT * FROM users WHERE username = 'administrator' AND password = '123';
  
  -- Modified query after injection
  SELECT * FROM users WHERE username = 'administrator' OR '1'='1' --' AND password = '123';
  ```
- The `' OR '1'='1'` makes the condition always true, and the `--` comments out the rest of the query (including the password check).
- This forces the database to return the "administrator" user’s data, bypassing authentication.

### 5. Solving the Lab
- After submitting the payload, I successfully logged in as "administrator" and accessed the admin panel, completing the lab.

## Payload Used
```sql
administrator' OR '1'='1' --
```

## Lessons Learned
- SQL injection can bypass authentication if user inputs are directly concatenated into SQL queries without sanitization.
- The use of comments (e.g., `--`) is a powerful technique to neutralize unwanted parts of a query.
- Testing with a known username (like "administrator") combined with logical manipulation (e.g., `OR '1'='1'`) is an effective strategy for login bypass attacks.

## Recommendations
- Use **prepared statements** or **parameterized queries** to prevent SQL injection.
- Implement **proper input validation** and **sanitization** for all user-supplied data.
- Avoid exposing **detailed error messages** that might reveal database structure.
- Use **Web Application Firewalls (WAFs)** to detect and block SQL injection attempts.
- Apply **least privilege access** to database accounts to minimize risks.

## Conclusion

This lab demonstrated how SQL injection can compromise authentication mechanisms. By injecting `administrator' OR '1'='1' --` into the username field, I bypassed the password check and gained access to the admin account. This exercise reinforced the importance of secure query handling in web applications.
