# #Description

Lab: SQL injection - **Visible error-based SQL injection**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed: 24 July 2026</br>

# #What was vulnerable:

The TrackingId cookie was vulnerable to error based SQL injection, the application displayed raw database error messages containing sensitive data directly on the page

# #What I did:

1. We intercept the request to the application with burp suite and see a cookie called TrackingId passed with the request. Adding a single quote at the end of the TrackingId cookie causes a visible  “`Unterminated string literal started at position 52 in SQL SELECT * FROM tracking WHERE id = 'bcolRp4ZozQfFgWY`'’ error giving us the exact query that the database is using.
2. Keeping the behavior of the site to display error messages from the database as they are in mind. We construct a query `' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--`  to get a error message with the username displayed in the message.
3. We modify the query to now show the password in the error message displayed on the site to get the login credentials of the administrator user. `' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)--` 

# #Why it worked:

The cookie value was concatenated directly into a SQL query without sanitisation or parameterisation.  The error message that the query returned was displayed on the site. The error message also contained the information on what the error was. So by using CAST function which converts a data type into another we try to convert the string data into int which gives us an error message with the exact string data we were trying to convert to integer. For example `' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--`  here the error read “`ERROR: invalid input syntax for type integer: "administrator"`” as the database encountered the error when converting “administrator” to int.

# #Impact:

An attacker could extract sensitive data directly from visible error messages, allowing full account takeover without needing blind enumeration techniques.

# #Fix:

1. Use parameterized queries - This forces the database to view user input only as data and not executable SQL code.
2. Validate input - Use of whitelists to only allow specific inputs to be given in the parameters
3. Don’t display error messages on the site. 

# #Screenshot:
# #Description

Lab: SQL injection - **Visible error-based SQL injection**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed: 23 July 2026</br>

# #What was vulnerable:

The TrackingId cookie was vulnerable to error based SQL injection, the application displayed raw database error messages containing sensitive data directly on the page

# #What I did:

1. We intercept the request to the application with burp suite and see a cookie called TrackingId passed with the request. Adding a single quote at the end of the TrackingId cookie causes a visible  “`Unterminated string literal started at position 52 in SQL SELECT * FROM tracking WHERE id = 'bcolRp4ZozQfFgWY`'’ error giving us the exact query that the database is using.
2. Keeping the behavior of the site to display error messages from the database as they are in mind. We construct a query `' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--`  to get a error message with the username displayed in the message.
3. We modify the query to now show the password in the error message displayed on the site to get the login credentials of the administrator user. `' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)--` 

# #Why it worked:

The cookie value was concatenated directly into a SQL query without sanitisation or parameterisation.  The error message that the query returned was displayed on the site. The error message also contained the information on what the error was. So by using CAST function which converts a data type into another we try to convert the string data into int which gives us an error message with the exact string data we were trying to convert to integer. For example `' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--`  here the error read “`ERROR: invalid input syntax for type integer: "administrator"`” as the database encountered the error when converting “administrator” to int.

# #Impact:

An attacker could extract sensitive data directly from visible error messages, allowing full account takeover without needing blind enumeration techniques.

# #Fix:

1. Use parameterized queries - This forces the database to view user input only as data and not executable SQL code.
2. Validate input - Use of whitelists to only allow specific inputs to be given in the parameters
3. Don’t display error messages on the site. 

# #Screenshot:
# #Description

Lab: SQL injection - **Visible error-based SQL injection**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed: 23 July 2026</br>

# #What was vulnerable:

The TrackingId cookie was vulnerable to error based SQL injection, the application displayed raw database error messages containing sensitive data directly on the page

# #What I did:

1. We intercept the request to the application with burp suite and see a cookie called TrackingId passed with the request. Adding a single quote at the end of the TrackingId cookie causes a visible  “`Unterminated string literal started at position 52 in SQL SELECT * FROM tracking WHERE id = 'bcolRp4ZozQfFgWY`'’ error giving us the exact query that the database is using.
2. Keeping the behavior of the site to display error messages from the database as they are in mind. We construct a query `' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--`  to get a error message with the username displayed in the message.
3. We modify the query to now show the password in the error message displayed on the site to get the login credentials of the administrator user. `' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)--` 

# #Why it worked:

The cookie value was concatenated directly into a SQL query without sanitisation or parameterisation.  The error message that the query returned was displayed on the site. The error message also contained the information on what the error was. So by using CAST function which converts a data type into another we try to convert the string data into int which gives us an error message with the exact string data we were trying to convert to integer. For example `' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--`  here the error read “`ERROR: invalid input syntax for type integer: "administrator"`” as the database encountered the error when converting “administrator” to int.

# #Impact:

An attacker could extract sensitive data directly from visible error messages, allowing full account takeover without needing blind enumeration techniques.

# #Fix:

1. Use parameterized queries - This forces the database to view user input only as data and not executable SQL code.
2. Validate input - Use of whitelists to only allow specific inputs to be given in the parameters
3. Don’t display error messages on the site. 

# #Screenshot:

!image.png# #Description

Lab: SQL injection - **Visible error-based SQL injection**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed: 23 July 2026</br>

# #What was vulnerable:

The TrackingId cookie was vulnerable to error based SQL injection, the application displayed raw database error messages containing sensitive data directly on the page

# #What I did:

1. We intercept the request to the application with burp suite and see a cookie called TrackingId passed with the request. Adding a single quote at the end of the TrackingId cookie causes a visible  “`Unterminated string literal started at position 52 in SQL SELECT * FROM tracking WHERE id = 'bcolRp4ZozQfFgWY`'’ error giving us the exact query that the database is using.
2. Keeping the behavior of the site to display error messages from the database as they are in mind. We construct a query `' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--`  to get a error message with the username displayed in the message.
3. We modify the query to now show the password in the error message displayed on the site to get the login credentials of the administrator user. `' AND 1=CAST((SELECT password FROM users LIMIT 1) AS int)--` 

# #Why it worked:

The cookie value was concatenated directly into a SQL query without sanitisation or parameterisation.  The error message that the query returned was displayed on the site. The error message also contained the information on what the error was. So by using CAST function which converts a data type into another we try to convert the string data into int which gives us an error message with the exact string data we were trying to convert to integer. For example `' AND 1=CAST((SELECT username FROM users LIMIT 1) AS int)--`  here the error read “`ERROR: invalid input syntax for type integer: "administrator"`” as the database encountered the error when converting “administrator” to int.

# #Impact:

An attacker could extract sensitive data directly from visible error messages, allowing full account takeover without needing blind enumeration techniques.

# #Fix:

1. Use parameterized queries - This forces the database to view user input only as data and not executable SQL code.
2. Validate input - Use of whitelists to only allow specific inputs to be given in the parameters
3. Don’t display error messages on the site. 

# #Screenshot:
<img width="1907" height="203" alt="image" src="https://github.com/user-attachments/assets/a92f4e35-d3c6-4a79-b6d4-4467dcb899d2" />
