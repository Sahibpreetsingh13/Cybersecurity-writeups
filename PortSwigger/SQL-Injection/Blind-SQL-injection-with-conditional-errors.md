# #Description

Lab: SQL injection -Blind SQL injection with conditional errors </br>
Platform: PortSwigger Web Academy</br>
Difficulty: Practitioner</br>
Date Completed: 24 July 2026</br>

# #What was vulnerable:

The trackingid cookie was vulnerable to a blind SQL injection allowing for the passwords of users to be compromised.

# #Prerequisites

1. Understanding of blind SQLi with conditional responses
2. Oracle specific syntax — dual table, ROWNUM, SUBSTR
3. Understanding of CASE WHEN statements in SQL
4. Familiarity with Burp Intruder for automation

# #What I did:

1. Identified that the `TrackingId` cookie value was being used in a SQL query server-side. Sending a single quote caused a error message but two single quotes `''` acts as an escaped quote in SQL, restoring valid syntax confirming the error was caused by an unmatched quote breaking the query structure
2. To confirm that the syntax error was an SQL error we appended TrackingId with `'||(SELECT '')||'`  the error was still present. For further testing we add a table name to the query `'||(SELECT '' FROM dual)||'`  and the error disappears confirming that the error was caused by invalid SQL syntax and also that the database is on Oracle.
3. To check if the users table exists in the database we add `'||(SELECT '' FROM users WHERE ROWNUM=1) ||'` . As no error was shown we can confirm that the table exists. (we use the ROWNUM=1 condition to prevent the query to return more than 1 row, which would break our concatenation.
4. We now create a query to exploit this behavior to test conditions by intentionally causing an error if the given condition is true by appending the TrackingId with `'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'`  as 1=1 is true we receive an error.
5. Using this query we first test if the username ‘administrator’ exists in the database `'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'` . We received an error confirming that the username administrator dos exist in the database.
6. To get the length of the password we modify the query `'||(SELECT CASE WHEN length(password) = 1 THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'` . And used Burp Intruder with a sequential number payload list testing values 1 through 30, identified the password length as 20 by finding the value that triggered a 500 error response.
7. We use the query `'||(SELECT CASE WHEN SUBSTR(password,1,1)='a' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'` . And use the burp intruder to get the first letter of the password. We repeat this process until we get the full password and log in to complete the lab

# #Why it worked:

The cookie value was concatenated directly into a SQL query without sanitisation or parameterisation.  An error message was displayed when the query returned an error using this behavior we used a CASE statement with an intentional error like TO_CHAR(1/0). Using this method let’s us test conditions like if the condition is true we get an error. We exploit this behavior to obtain the password for any user. Unlike boolean based blind SQLi which relies on visible content differences, this technique uses error vs no error as the oracle making it applicable even when the application returns identical page content for true and false conditions.

# #Impact:

An attacker could extract arbitrary data from the database, including other users' credentials, without needing any visible error messages or direct output. This ultimately allowed full account takeover of the administrator account, and the technique could be extended to dump the entire database.

# #Fix:

1. Use parameterized queries - This forces the database to view user input only as data and not executable SQL code.
2. Validate input - Use of whitelists to only allow specific inputs to be given in the parameters
3. Disable detailed error messages in production error visibility enabled the initial identification of the injection point and confirmed Oracle database type

# #Screenshot:
# #Description

Lab: SQL injection -Blind SQL injection with conditional errors </br>
Platform: PortSwigger Web Academy</br>
Difficulty: Practitioner</br>
Date Completed: 23 July 2026</br>

# #What was vulnerable:

The trackingid cookie was vulnerable to a blind SQL injection allowing for the passwords of users to be compromised.

# #Prerequisites

1. Understanding of blind SQLi with conditional responses
2. Oracle specific syntax — dual table, ROWNUM, SUBSTR
3. Understanding of CASE WHEN statements in SQL
4. Familiarity with Burp Intruder for automation

# #What I did:

1. Identified that the `TrackingId` cookie value was being used in a SQL query server-side. Sending a single quote caused a error message but two single quotes `''` acts as an escaped quote in SQL, restoring valid syntax confirming the error was caused by an unmatched quote breaking the query structure
2. To confirm that the syntax error was an SQL error we appended TrackingId with `'||(SELECT '')||'`  the error was still present. For further testing we add a table name to the query `'||(SELECT '' FROM dual)||'`  and the error disappears confirming that the error was caused by invalid SQL syntax and also that the database is on Oracle.
3. To check if the users table exists in the database we add `'||(SELECT '' FROM users WHERE ROWNUM=1) ||'` . As no error was shown we can confirm that the table exists. (we use the ROWNUM=1 condition to prevent the query to return more than 1 row, which would break our concatenation.
4. We now create a query to exploit this behavior to test conditions by intentionally causing an error if the given condition is true by appending the TrackingId with `'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'`  as 1=1 is true we receive an error.
5. Using this query we first test if the username ‘administrator’ exists in the database `'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'` . We received an error confirming that the username administrator dos exist in the database.
6. To get the length of the password we modify the query `'||(SELECT CASE WHEN length(password) = 1 THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'` . And used Burp Intruder with a sequential number payload list testing values 1 through 30, identified the password length as 20 by finding the value that triggered a 500 error response.
7. We use the query `'||(SELECT CASE WHEN SUBSTR(password,1,1)='a' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'` . And use the burp intruder to get the first letter of the password. We repeat this process until we get the full password and log in to complete the lab

# #Why it worked:

The cookie value was concatenated directly into a SQL query without sanitisation or parameterisation.  An error message was displayed when the query returned an error using this behavior we used a CASE statement with an intentional error like TO_CHAR(1/0). Using this method let’s us test conditions like if the condition is true we get an error. We exploit this behavior to obtain the password for any user. Unlike boolean based blind SQLi which relies on visible content differences, this technique uses error vs no error as the oracle making it applicable even when the application returns identical page content for true and false conditions.

# #Impact:

An attacker could extract arbitrary data from the database, including other users' credentials, without needing any visible error messages or direct output. This ultimately allowed full account takeover of the administrator account, and the technique could be extended to dump the entire database.

# #Fix:

1. Use parameterized queries - This forces the database to view user input only as data and not executable SQL code.
2. Validate input - Use of whitelists to only allow specific inputs to be given in the parameters
3. Disable detailed error messages in production error visibility enabled the initial identification of the injection point and confirmed Oracle database type

# #Screenshot:
<img width="1907" height="203" alt="image" src="https://github.com/user-attachments/assets/2d8cf794-f4b0-410e-835b-8c4f29ed4bfc" />

