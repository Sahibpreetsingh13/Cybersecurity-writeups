# # Description

Lab:  Authentication -  **Username enumeration via subtly different responses**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  01 October 2026</br>

# # What was vulnerable:

The application was vulnerable to username enumeration and password brute-force attacks as it gave a subtly different response to if just the username was incorrect to if both the username and password was incorrect. 

# # What I did:

1. Opened burp suite and tried to log in with random credentials.
2. Intercepted the request in burp suite and sent the request to burp intruder.
3. Used Sniper attack type with the username list (provided by the lab) as payload, Sniper tests one payload position at a time which is correct here since we're only fuzzing the username field while keeping the password constant.
4. We also set the grep-extract parameter in the settings to fetch the error message alongside the attack so that when the attack finishes we can easily find the difference between the error messages.
5. After the attack finished we noticed that one error message for a response displayed “Invalid username and password” instead of “Invalid username and password.”.
6. The result from this attempt is our username , using this username we do a sniper attack again but for the password field.
7. Observed that one attempt returned a 302 redirect response instead of the 200 response returned for failed attempts, confirming the correct password.
8. Used the username and password found to solve the lab.

# # Why it worked:

This is called username enumeration a common authentication flaw where differing application responses leak whether a username exists, reducing a brute force attack from needing to guess both username and password simultaneously to only needing to guess the password.

# # Impact:

An attacker with valid credentials can access all functionality available to that account for administrator accounts this means full application control, user management, and potentially access to internal infrastructure and sensitive data.

# # Fix:

1. Do not display different error messages for different scenarios like the password being wrong or the username being wrong.
2. Limit the rate of requests to the login page - This will prevent a brute force attack as a threat actor won’t be able to send so many concurrent requests.
3. Implement account lockout or CAPTCHA after a set number of failed attempts this directly prevents automated brute force attacks regardless of response message consistency

# # Screenshot:
<img width="1730" height="200" alt="image" src="https://github.com/user-attachments/assets/12cadb80-e651-4ac9-9318-ddb47f6518e5" />
