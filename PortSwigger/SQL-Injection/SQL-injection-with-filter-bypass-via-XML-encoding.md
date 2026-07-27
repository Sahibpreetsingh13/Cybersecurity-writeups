# #Description

Lab: SQL injection - **SQL injection with filter bypass via XML encoding** </br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed: 27July 2026</br>

# #Prerequisites

1. Understanding of UNION attacks and column enumeration
2. Understanding of string concatenation for single column extraction
3. Knowledge of WAF bypass techniques encoding methods
4. Familiarity with Hackvertor or manual hex encoding

# #What was vulnerable:

The Store Id parameter in the XML body of the stock check request was vulnerable to SQL injection WAF/blacklist filtering was bypassable through hex encoding of the payload

# #What I did:

1. Opened burp suite and intercepted the requests sent to the application.
2. Observed that stock check feature sends the `productId` ,`StoreId` to the application in XML format. And the `StoreId`  takes in a variable.
3. To test for a SQLi vulnerability we send a mathematical expression as the input in the `StoreId`  and observed that the expression was calculated in the response
4. Crafted a UNION attack `UNION SELECT NULL` to see the no of columns returned by the original query.
5. The request was not processed and the response received read “Attack Detected”  likely  indicating  a black list or a firewall in place to detect SQLi attacks in the application.
6. Used the Hackvertor extension in Burp Suite to hex encode the payload converting each character to its hex representation bypasses the WAF because it checks for plaintext SQL keywords like UNION and SELECT but doesn't decode hex before checking.
7. Observed that the no of columns returned by the original query is 1. keeping that in mind we construct a query to get the username and password from the users database `1 UNION SELECT username || '~' || password FROM users`  and hex encoded this as well.
8. Used the extracted credentials to log into the administrator account and complete the lab.

# #Why it worked:

UNION attack mechanics documented in previous writeups. The key technique here was hex encoding the WAF performed pattern matching on raw input without decoding, so encoded payloads bypassed detection entirely while the database decoded and executed them normally.

# #Impact:

The threat actor can steal sensitive information from the database like the username, password in this case 

# #Fix:

1. Use parameterized queries - This forces the database to view user input only as data and not executable SQL code.
2. Validate input - Use of whitelists to only allow specific inputs to be given in the parameters
3. Always use a white list/allow list only allowing the intended input rather than trying to block attacks with a black list/deny list. 

# #Screenshot:
# #Description

Lab: SQL injection - **SQL injection with filter bypass via XML encoding** </br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed: 27July 2026</br>

# #Prerequisites

1. Understanding of UNION attacks and column enumeration
2. Understanding of string concatenation for single column extraction
3. Knowledge of WAF bypass techniques encoding methods
4. Familiarity with Hackvertor or manual hex encoding

# #What was vulnerable:

The Store Id parameter in the XML body of the stock check request was vulnerable to SQL injection WAF/blacklist filtering was bypassable through hex encoding of the payload

# #What I did:

1. Opened burp suite and intercepted the requests sent to the application.
2. Observed that stock check feature sends the `productId` ,`StoreId` to the application in XML format. And the `StoreId`  takes in a variable.
3. To test for a SQLi vulnerability we send a mathematical expression as the input in the `StoreId`  and observed that the expression was calculated in the response
4. Crafted a UNION attack `UNION SELECT NULL` to see the no of columns returned by the original query.
5. The request was not processed and the response received read “Attack Detected”  likely  indicating  a black list or a firewall in place to detect SQLi attacks in the application.
6. Used the Hackvertor extension in Burp Suite to hex encode the payload converting each character to its hex representation bypasses the WAF because it checks for plaintext SQL keywords like UNION and SELECT but doesn't decode hex before checking.
7. Observed that the no of columns returned by the original query is 1. keeping that in mind we construct a query to get the username and password from the users database `1 UNION SELECT username || '~' || password FROM users`  and hex encoded this as well.
8. Used the extracted credentials to log into the administrator account and complete the lab.

# #Why it worked:

UNION attack mechanics documented in previous writeups. The key technique here was hex encoding the WAF performed pattern matching on raw input without decoding, so encoded payloads bypassed detection entirely while the database decoded and executed them normally.

# #Impact:

The threat actor can steal sensitive information from the database like the username, password in this case 

# #Fix:

1. Use parameterized queries - This forces the database to view user input only as data and not executable SQL code.
2. Validate input - Use of whitelists to only allow specific inputs to be given in the parameters
3. Always use a white list/allow list only allowing the intended input rather than trying to block attacks with a black list/deny list. 

# #Screenshot:
<img width="1645" height="196" alt="image" src="https://github.com/user-attachments/assets/47394fe2-bcf2-41ef-ba77-e50b1a6cd470" />
