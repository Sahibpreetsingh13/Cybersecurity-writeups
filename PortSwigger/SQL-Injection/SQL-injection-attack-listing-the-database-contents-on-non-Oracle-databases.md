# # Description

Lab: SQL injection -**SQL injection attack, listing the database contents on non-Oracle databases**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  23 July 2026</br>

# # What was vulnerable:

The category parameter was vulnerable to SQL injection, allowing UNION based attacks to retrieve data from arbitrary tables within the database.

# #Prerequites

Before we solve the lab we need to know:

1. How to determine the number of columns for a UNION attack
2. How to determine the type of data in the columns for the UNION attack

# # What I did:

1. We query the database to determine the no of columns for the UNION attack. The query used was: `'+UNION+SELECT+NULL--` 
2. After determining that the query returned 2 columns we tested both columns `'+UNION+SELECT+NULL,'abc'--` returned successfully confirming that both the columns contains text. 
3. For the next step we need to query the information_schema to know the tables present in the database. For that we use the following query:
    
    `'+UNION+SELECT+table_name,NULL+FROM+information_schema.tables--` 
    
4. From the previous query we got to know that the table with the information about the users is called ‘users_wwshkg’ in the database.
5. Using this information we construct another query to know the columns in the table ‘users_wwshkg’. The query used is:
    
    `'+UNION+SELECT+column_name,NULL+FROM+information_schema.columns+WHERE+table_name='users_wwshkg'--` 
    
6. The query returned the column names username_wvrmwv, password_rmrgia and email but as the original query just has two columns we omit the email column for now.(if you want the emails of all the users you can make an additional query with the username and email columns).
7. With the given information we construct a query to retrieve the username and passwords of all the users in the database.
    
    `'+UNION+SELECT+username_wvrmwv,password_rmrgia+FROM+users_wwshkg--` 
    
8. We got the credentials for the administrator account and used them to log in and complete the lab.

# # Why it worked:

1. Column count and data type determined using NULL method and string injection as documented in previous UNION attack writeups.
2. The query used to get the information about the tables and columns in the database
    
    Most database types (except oracle) have a set of views called infomation_schema that contains all the information regarding the database. So to know the names of the tables in the database we altered the query to get ‘table_name’ from information_schema.tables which is the view in information_schema that contains the information about the names of every table in the database. After getting the name of the table containing the users and passwords we queried the information_schema.columns of the users table by using the query `FROM+information_schema.columns+WHERE+table_name='users_wwshkg'--`  in which WHERE is used to set a condition to be met by the database. We did this to get the names of the columns present in the users table to construct the final attack.
    
3. The query to retrieve the credentials of the users.
    
    `'` = This is used as a early completion for the previous query.
    
    `UNION` - It is used to combine the results of two or more SELECT statements.
    
    `SELECT` - This is our second SELECT statement beside the original query 
    
    `username_wvrmwv, password_rmrgia` - These are the columns containing the data that we need.
    
    `FROM` - This is a command in SQL which is used to tell which table in the database do the columns asked belong to.
    
    `users--` - users is the name of the database table and the — is used to write a comment in SQL using it here comments out the rest of the query to the database.
    

# # Impact:

A UNION attack can lead to sensitive information like the username, passwords in this case being leaked.

# # Fix:

1. Use parameterized queries - This forces the database to view user input only as data and not executable SQL code.
2. Validate input - Use of whitelists to only allow specific inputs to be given in the parameters
3. Secure passwords in database- Do not store sensitive information as plain text in the database. Instead use a hashing algorithm and store the hash instead.

# # Screenshot:
<img width="1886" height="198" alt="image" src="https://github.com/user-attachments/assets/1da0647a-6475-40cf-9f84-26052efc8554" />
