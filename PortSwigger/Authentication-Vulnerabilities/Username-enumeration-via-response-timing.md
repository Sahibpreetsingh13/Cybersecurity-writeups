# # Description

Lab:  Authentication - **Username enumeration via response timing**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  01 October 2026</br>

# # What was vulnerable:

The application was vulnerable to username enumeration and password brute-force attacks as it took significantly longer time to respond if the username was correct as oppose to if the username was not correct. The application also implemented an IP-based rate limit that trusted the client-supplied X-Forwarded-For header, allowing it to be bypassed by spoofing a different IP on every request.

# # What I did:

1. Opened burp suite and tried to log in with random credentials noted that the application blocks the requesting ip after a number of incorrect attempts.
2. Intercepted the request in burp suite and sent the request to burp intruder.
3. Used Pitchfork attack type with the username list (provided by the lab) as payload and numbers in sequence for the added X-Forwarded-For header to spoof different ip addresses, A pitchfork attack takes more than one payload position  and iterates over both/ all the positions simultaneously.
4. Used a 100 character password because the application likely compared the password against the stored hash character by character or processed it through a slower code path only when the username was valid a longer input exaggerates this processing time difference, making the timing signal easier to detect over network jitter.
5. After the attack finished we noticed that one response took significantly longer than the others we repeat the same attack to confirm the hypothesis.
6. The result from this attempt is our username , using this username we do a pitchfork attack again but for the password field and the X-Forwarded-For field.
7. Observed that one attempt returned a 302 redirect response instead of the 200 response returned for failed attempts, confirming the correct password.
8. Used the username and password found to solve the lab.

# # Why it worked:

This is called username enumeration a common authentication flaw where differing application responds leak whether a username exists, reducing a brute force attack from needing to guess both username and password simultaneously to only needing to guess the password. The IP based firewall was not set up correctly as X-Forwarded-For header was allowed by the application. When the username existed, the application proceeded to hash or compare the submitted password against the stored value an operation that takes measurable time. When the username didn't exist, the application returned immediately without performing this comparison, creating a consistent timing gap that reveals valid username.

# # Impact:

An attacker with valid credentials can access all functionality available to that account for administrator accounts this means full application control, user management, and potentially access to internal infrastructure and sensitive data.

# # Fix:

1. Do not display different error messages for different scenarios like the password being wrong or the username being wrong.
2. Limit the rate of requests to the login page - This will prevent a brute force attack as a threat actor won’t be able to send so many concurrent requests.
3. Implement account lockout or CAPTCHA after a set number of failed attempts this directly prevents automated brute force attacks regardless of response message consistency
4. Do not allow the X-Forwarded-For header if using an IP based firewall to block requests.
5. Implement an algorithm that takes the same time to respond whether the username is correct or not.

# # Screenshot:
<img width="1512" height="207" alt="image" src="https://github.com/user-attachments/assets/61f434ce-8574-4fc9-967c-eafe38659ac1" />
