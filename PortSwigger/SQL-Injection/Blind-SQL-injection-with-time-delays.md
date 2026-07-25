# #Description

Lab: SQL injection - Blind SQL injection with time delays</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed: 25 July 2026</br>

# #What was vulnerable:

The TrackingId cookie was vulnerable to a blind SQL injection where no message or error was displayed but delaying the query caused the http response to be delayed as well 

# #Prerequisites

1. Understanding of blind SQLi with conditional responses
2. PostgreSQL specific syntax pg_sleep(), CASE WHEN
3. Familiarity with Burp Intruder for automating character extraction
4. Understanding that time based techniques apply when
no visible response difference exists

# #What I did:

1. Intercepted the request to the application using burp suite and saw a cookie called TrackingId.
2. Sent the query `';SELECT+CASE+WHEN+(1=1)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END--` .The `;` acts as a query terminator, ending the original SELECT statement and starting a new independent query — this is called a stacked query and is supported by PostgreSQL and observed that the response was delayed by 10 seconds confirming a time delay blind SQL injection.
3. Constructed a query to check if the username ‘administrator’ exists in the database. The query was `';SELECT+CASE+WHEN+(username='administrator')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--` . The response was delayed again confirming that the username exists in the database
4. Modified the query to get the length of the password `';SELECT+CASE+WHEN (username='administrator' AND length(password)=1)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--` . Configured Burp Intruder with a sequential number payload 1 through 30, identified the correct length by filtering for responses with more than 10 second response time using the Columns → Response received timer.
5. Using the same logic and `';SELECT+CASE+WHEN+(username='administrator' AND SUBSTRING(password,1,1)='a')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--`  this query, We iterate through every character in every index to get the password. Using the obtained credentials we login to the ‘administrator’ account and complete the lab.

# #Why it worked:

The cookie value was concatenated directly into a SQL query without sanitisation or parameterisation. SQL queries are normally processed synchronously by the application, delaying the execution of a SQL query also delays the HTTP response. We cause this delay with the function `pg_sleep(10)`  to intentionally delay the response by 10 seconds and a `CASE` function to trigger the delay only if  a certain condition is true, Giving us information of whether the statement in true or not.(`pg_sleep(10)` is PostgreSQL specific  equivalent functions exist for other databases: `SLEEP(10)`for MySQL, `WAITFOR DELAY(0:0:10)` for MSSQL, demonstrating why database fingerprinting matters before choosing payloads).

# #Impact:

An attacker could extract sensitive data without needing any visible error or message.

# #Fix:

1. Use parameterized queries - This forces the database to view user input only as data and not executable SQL code.
2. Validate input - Use of whitelists to only allow specific inputs to be given in the parameters
3. Implement query timeouts setting maximum query execution times limits the effectiveness of time based attacks even if injection exists

# #Screenshot:
<img width="1890" height="200" alt="image" src="https://github.com/user-attachments/assets/9adef214-2598-4b0a-9838-9cd8f118c9a5" />
