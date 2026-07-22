# # Description

Lab: SQL injection -**SQL injection attack, querying the database type and version on MySQL and Microsoft** </br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  22 July 2026</br>

# # What was vulnerable:

The category parameter was vulnerable to SQL injection, exposing the underlying query structure to UNION based attacks which exposed the version and type of database used.

# #Prerequisites

1. How to determine the number of columns for a UNION attack
2. How to determine the data type of columns
3. Knowledge of database specific syntax —
@@version for MySQL/Microsoft, # for comments
vs -- for Oracle/PostgreSQL

# # What I did:

To check the version of the database we first need to check the no of columns for the UNION attack with the query `'+UNION+SELECT+NULL,NULL#`  .This told us that the UNION attack required two columns. The lab specified MySQL ( in a real scenario database type can be identified through error messages, response behavior, or by testing database specific syntax until one succeeds) we modified the previous query `'+UNION+SELECT+@@version,NULL#`  to get the information about the database type and version.

# # Why it worked:

`'` = This is used as a early completion for the previous query.

`UNION` - It is used to combine the results of two or more SELECT statements.

`SELECT` - This is our second SELECT statement beside the original query 

`@@version` - This is the command used in MySQL to get the database details(this command varies with the type of SQL used).Knowing the exact database version allows an attacker to research known vulnerabilities specific to that version, making this a critical reconnaissance step before deeper exploitation.

`,NULL#` -NULL acts as a placeholder as the UNION attack required two columns and # is used to start a comment in MySQL.

# # Impact:

This technique is a prerequisite for data extraction as you need to gather information about the database like its type and version to test the vulnerabilities present on the particular version or type of database.

# # Fix:

1. Use parameterized queries - This forces the database to view user input only as data and not executable SQL code.
2. Validate input - Use of whitelists to only allow specific inputs to be given in the parameters

# # Screenshot:
<img width="1907" height="196" alt="image" src="https://github.com/user-attachments/assets/e53e8065-787f-493e-b9a6-f7951b5ccabe" />
