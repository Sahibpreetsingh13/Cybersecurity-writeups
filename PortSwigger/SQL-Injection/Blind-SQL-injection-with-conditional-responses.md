# #Description

Lab: SQL injection -Blind SQL injection with conditional responses</br>
Platform: PortSwigger Web Academy</br>
Difficulty: Practitioner</br>
Date Completed: 22 July 2026</br>

# #What was vulnerable:

The trackingid cookie was vulnerable to a blind SQL injection allowing for the passwords of users to be compromised.

# #What I did:

1. Identified that the `TrackingId` cookie value was being used in a SQL query server-side. Sending a single quote caused a delayed/altered response, confirming the injection point.
2. Confirmed the query was a blind injection with conditional responses — when a boolean condition evaluated to `TRUE`, the app displayed a "Welcome back" message; when `FALSE`, it did not (no errors or data returned directly).
3. Used this behaviour to enumerate data one bit at a time. First confirmed the `users` table exists, then confirmed a row existed where `username = 'administrator'`.
4. Used a binary search technique with `SUBSTRING()` and conditional statements (e.g. `AND SUBSTRING(password,1,1) > 'm'`) to narrow down each character of the administrator's password, sending repeated requests via Burp Repeater/Intruder and checking for the presence of the "Welcome back" string.
5. Repeated this process for each character position until the full password was recovered, then logged in as `administrator` using the extracted password.

# #Why it worked:

The cookie value was concatenated directly into a SQL query without sanitisation or parameterisation. Although the application didn't return database errors or data directly, it leaked a single bit of information (true/false) through a visible difference in the response for each injected condition — enough to fully reconstruct sensitive data through repeated, automatable requests.

# #Impact:

An attacker could extract arbitrary data from the database, including other users' credentials, without needing any visible error messages or direct output. This ultimately allowed full account takeover of the administrator account, and the technique could be extended to dump the entire database.

# #Fix:

1. Use parameterized queries - This forces the database to view user input only as data and not executable SQL code.
2. Validate input - Use of whitelists to only allow specific inputs to be given in the parameters

# #Screenshot:
<img width="1897" height="202" alt="image" src="https://github.com/user-attachments/assets/a4b977c1-0832-4b23-ad4e-7b9754fc680c" />
