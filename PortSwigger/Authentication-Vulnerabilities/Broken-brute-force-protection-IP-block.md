# # Description

Lab:  Authentication - Broken brute-force protection, IP block</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  03 October 2026</br>

# # What was vulnerable:

The application had a brute force protection which blocked the IP of the device for some time after it had made a number of incorrect password guesses but it was not implemented properly as entering the correct password reset the no of guesses which could be exploited.

# # What I did:

1. Opened burp suite and  logged into the application using the credentials provided.
2. Intercepted the request using burp suite and sent it to the burp intruder.
3. Using a python program compiled a list of usernames alternating between “wiener” ( the username for the test account) and “carlos” (the account we want to brute force the password for)
4. We also compiled a list of passwords alternating between the passwords provided by the lab and “peter” ( the password for the given test account).
5. Using the pitchfork attack with both username and password as payload positions and the two compiled as the payload we launch an attack.
6. In the resource pool settings we changed the maximum concurrent requests to 1 so that the attack commences in a sequence 
7. Observed that one attempt returned a 302 redirect response for the username carlos  instead of the 200 response returned for failed attempts, confirming the correct password.
8. Used the correct password to solve the lab.

# # Why it worked:

The app blocked an IP after a threshold of failed attempts, but any successful login  regardless of which account reset that counter to zero. By interleaving one guess against `carlos` with one correct `wiener:peter` login, we never accumulated more than a single failed attempt before it was wiped, giving us effectively unlimited guesses against `carlos` despite the protection being 'in place.'

# # Impact:

An attacker with valid credentials can access all functionality available to that account for administrator accounts this means full application control, user management, and potentially access to internal infrastructure and sensitive data.

# # Fix:

1. Limit the rate of requests to the login page - This will prevent a brute force attack as a threat actor won’t be able to send so many concurrent requests.
2. Implement account lockout or CAPTCHA after a set number of failed attempts this directly prevents automated brute force attacks regardless of response message consistency
3. Do not reset the failed-attempt counter for an IP upon any successful login tie the counter to the specific account being targeted, or use a threshold that only resets after a timeout, not on-demand via an unrelated successful login

# # Screenshot:
<img width="1411" height="200" alt="image" src="https://github.com/user-attachments/assets/e4ea4da3-7112-4873-8ba0-dd6b3eb35e80" />
