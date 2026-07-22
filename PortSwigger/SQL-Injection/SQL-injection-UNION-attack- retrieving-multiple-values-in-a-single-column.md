# # Description

Lab: SQL injection -**SQL injection UNION attack, retrieving multiple values in a single column**</br>

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

1. We query the database to determine the no of columns for the UNION attack. The query used was: `'+UNION+SELECT+NULL--` 
2. After determining that the query returned 2 columns we tested both columns `'+UNION+SELECT+,NULL,'abc'--` returned successfully confirming column 2 contains text, `'+UNION+SELECT+'abc',NULL--` threw an error confirming column 1 does not.

3. Only one of the two columns contained text data so it was not possible to retrieve two data columns for the attack 
4. keeping the constrain in mind we construct the attack:
    
    `'+UNION+SELECT+NULL,username||'~'||password+FROM+users—`
    
5. Using the credentials provided by the query we logged into the administrator account to complete the lab.

# # Why it worked:

`'` = This is used as a early completion for the previous query.

`UNION` - It is used to combine the results of two or more SELECT statements.

`SELECT` - This is our second SELECT statement beside the original query 

`username,password` - These are the columns containing the data that we need.

`FROM` - This is a command in SQL which is used to tell which table in the database do the columns asked belong to.

`||'~'||`  - The ‘||’ (double pipe) operator acts as a string concatenator. The injected query concatenates together the values of the `username` and `password` fields, separated by the `~` character. Allowing us to only use one column to get both the username and password.

`users--` - users is the name of the database table and the — is used to write a comment in SQL using it here comments out the rest of the query to the database.

# # Impact:

A UNION attack can lead to sensitive information like the username, passwords in this case being leaked.

# # Fix:

1. Use parameterized queries - This forces the database to view user input only as data and not executable SQL code.
2. Validate input - Use of whitelists to only allow specific inputs to be given in the parameters
3. Secure passwords in database- Do not store sensitive information as plain text in the database. Instead use a hashing algorithm and store the hash instead.

# # Screenshot:
<img width="1882" height="195" alt="image" src="https://github.com/user-attachments/assets/fdeddbfc-e712-41e8-aa65-dca0b0055d0e" />
