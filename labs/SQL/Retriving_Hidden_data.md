# SQL Injection Lab Write-Up: Retrieving Hidden Data

## Lab Overview

This write-up documents my solution to the **"SQL injection vulnerability in WHERE clause allowing retrieval of hidden data"** lab from PortSwigger's Web Security Academy. The goal of this lab is to exploit an SQL injection vulnerability to retrieve all products, including those not meant to be displayed in a specific category.

## Objective

The objective is to bypass the application's filtering mechanism and retrieve hidden data (products) from the database by injecting malicious SQL code into a vulnerable parameter.

## Steps to Solve

### 1. Exploring the Application

- I started by interacting with the web application to understand how it works.
- The application has a product listing page with a category filter (e.g., `Gifts`, `Accessories`).
- Clicking on a category sends a request to the server, likely querying a database to fetch products in that category.

### 2. Identifying the Vulnerable Parameter

- I tested different parts of the application to find a parameter that interacts with the database and returns data.
- After some trial and error, I noticed the URL structure when selecting a category:
  
  ```plaintext
  https://example.web-security-academy.net/filter?category=Gifts
  ```

- The `category` parameter seemed promising because it likely gets inserted into an SQL query to filter products.

### 3. Testing for SQL Injection

- To confirm if the `category` parameter is vulnerable, I modified the input to include a simple SQL injection payload.
- I replaced `Gifts` with the following payload:
  
  ```sql
  ' OR '1'='1
  ```
  
- The full URL became:
  
  ```plaintext
  https://example.web-security-academy.net/filter?category=' OR '1'='1
  ```

- After submitting this, the page returned **all products** from the database, not just those in a specific category.

### 4. Understanding the Payload

- The payload `' OR '1'='1` works by altering the SQL query's logic:
  
  **Original query (assumed):**
  
  ```sql
  SELECT * FROM products WHERE category = 'Gifts';
  ```
  
  **Modified query after injection:**
  
  ```sql
  SELECT * FROM products WHERE category = '' OR '1'='1';
  ```

- The condition `'1'='1'` is always true, so the query returns **all rows** from the `products` table.

### 5. Solving the Lab

- Submitting the payload successfully displayed all hidden products, completing the lab's objective.

## Payload Used

```sql
' OR '1'='1
```

## Lessons Learned

- SQL injection vulnerabilities occur when user input is not properly sanitized before being included in a database query.
- Simple payloads like `' OR '1'='1` can bypass weak input validation and expose sensitive data.
- Testing different parameters systematically is key to identifying injection points.

## Recommendations

- Use **parameterized queries** (prepared statements) to prevent SQL injection.
- Sanitize and validate all user inputs on the **server side**.

## Conclusion

This lab was a great introduction to SQL injection. By experimenting with the `category` parameter and crafting a basic payload, I was able to retrieve hidden data from the database. This exercise highlights the importance of **secure coding practices** in web development.

