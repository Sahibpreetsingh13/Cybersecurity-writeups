# # Description

Lab: SQL injection - **SQL injection UNION attack, determining the number of columns returned by the query**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  19 July 2026</br>

# # What was vulnerable:

The category parameter was vulnerable to SQL injection, exposing the underlying query structure to UNION based attacks.

# # What I did:

The objective of the lab was to find the number of columns returned by the query containing the category parameter. The first step was to add `'+UNION+SELECT+NULL--`  at the end of the category parameter. This gave an internal error message, we keep adding NULL like `'+UNION+SELECT+NULL,NULL--` until it stops giving an error message which in this case was 3 NULLs which tells us the number of columns used by the query.

# # Why it worked:

`UNION` - It is used to combine the results of two or more SELECT statements.

`SELECT` - This is our second SELECT statement beside the original query 

`NULL` - We used NULL because it is convertible to every common data type, so it maximizes the chance that the payload will succeed when the column count is correct.

The UNION operator needs the same number of columns NULL is used specifically to bypass type matching requirements in both the SELECT statements to give an output. Failure to meet this condition results in an error message so by adding `'+UNION+SELECT+NULL--`  we test for this condition to be met which will be indicated with the query giving no error message so we add more NULLs to the query until we reach the same number of NULLs as the number of columns in the original query.

# # Impact:

This technique is a prerequisite for data extraction knowing the column count enables further UNION attacks to retrieve sensitive database information.

# # Fix:

1. Use parameterized queries - This forces the database to view user input only as data and not executable SQL code.
2. Validate input - Use of whitelists to only allow specific inputs to be given in the parameters

# # Screenshot:
<img width="1873" height="197" alt="image" src="https://github.com/user-attachments/assets/0cc08216-d48f-405f-8ac7-c67e5fc7abb0" />
