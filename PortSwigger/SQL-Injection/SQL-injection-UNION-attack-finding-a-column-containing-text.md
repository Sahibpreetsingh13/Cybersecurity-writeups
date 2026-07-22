# # Description

Lab: SQL injection -**SQL injection UNION attack, finding a column containing text** </br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  22 July 2026</br>

# # What was vulnerable:

The category parameter was vulnerable to SQL injection, exposing the underlying query structure to UNION based attacks.

# # What I did:

The objective of the lab was to find the number of columns returned by the query containing the category parameter and also find a column containing text . The first step was to add `'+UNION+SELECT+NULL--`  at the end of the category parameter. This gave an internal error message, we keep adding NULL like `'+UNION+SELECT+NULL,NULL--` until it stops giving an error message which in this case was 3 NULLs which tells us the number of columns used by the query. For the second step replaced each NULL with `'a'` one at a time `'+UNION+SELECT+'a',NULL,NULL--` then `'+UNION+SELECT+NULL,'a',NULL--` then `'+UNION+SELECT+NULL,NULL,'a'--` the second position returned successfully confirming it holds text data type.

# # Why it worked:

`UNION` - It is used to combine the results of two or more SELECT statements.

`SELECT` - This is our second SELECT statement beside the original query 

`NULL` - We used NULL because it is convertible to every common data type, so it maximizes the chance that the payload will succeed when the column count is correct.

The UNION operator requires the same number of columns in both SELECT statements. NULL is used specifically to bypass type matching requirements, maximizing compatibility across different data types. Failure to meet this condition results in an error message so by adding `'+UNION+SELECT+NULL--`  we test for this condition to be met which will be indicated with the query giving no error message so we add more NULLs to the query until we reach the same number of NULLs as the number of columns in the original query.

Replacing NULL with the string `'a'` forces the database to insert a text value into that column position. If the column holds a non-text data type like integer the database throws a type mismatch error. A successful response confirms the column accepts text, making it usable for string data extraction in subsequent attacks.

# # Impact:

This technique is a prerequisite for data extraction knowing the column count and the data type of the column enables further UNION attacks to retrieve sensitive database information.

# # Fix:

1. Use parameterized queries - This forces the database to view user input only as data and not executable SQL code.
2. Validate input - Use of whitelists to only allow specific inputs to be given in the parameters

# # Screenshot:
<img width="1892" height="257" alt="image" src="https://github.com/user-attachments/assets/b77794b7-17ec-4421-aa9c-b7307148b9b9" />
