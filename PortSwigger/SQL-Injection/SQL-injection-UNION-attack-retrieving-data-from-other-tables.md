# # Description

Lab: SQL injection - **SQL injection UNION attack, retrieving data from other tables**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  22 July 2026</br>

# # What was vulnerable:

The category parameter was vulnerable to SQL injection, allowing UNION based attacks to retrieve data from arbitrary tables within the database.

# #Prerequites

Before we solve the lab we need to know:

1. How to determine the number of columns for a UNION attack
2. How to determine the type of data in the columns for the UNION attack
3. The useful tables present in the database and their column names  discoverable through information_schema queries in a real attack

For this lab we where given the information that the table users contains the columns username and password.

# # What I did:

The lab provided that the query returns 2 text columns and the users table contains username and password columns. Injected `'+UNION+SELECT+username,password+FROM+users--` into the category parameter which returned credentials for all users including the administrator. Used the retrieved credentials to log in and complete the lab.

# # Why it worked:

`'` = This is used as a early completion for the previous query.

`UNION` - It is used to combine the results of two or more SELECT statements.

`SELECT` - This is our second SELECT statement beside the original query 

`username,password` - These are the columns containing the data that we need.

`FROM` - This is a command in SQL which is used to tell which table in the database do the columns asked belong to.

`users--` - users is the name of the database table and the — is used to write a comment in SQL using it here comments out the rest of the query to the database.

# # Impact:

A UNION attack can lead to sensitive information like the username, passwords in this case being leaked.

# # Fix:

1. Use parameterized queries - This forces the database to view user input only as data and not executable SQL code.
2. Validate input - Use of whitelists to only allow specific inputs to be given in the parameters
3. Secure passwords in database- Do not store sensitive information as plain text in the database. Instead use a hashing algorithm and store the hash instead.

# # Screenshot:
<img width="1888" height="201" alt="image" src="https://github.com/user-attachments/assets/128edb4c-4f3e-49a2-a710-3eac697485a1" />
