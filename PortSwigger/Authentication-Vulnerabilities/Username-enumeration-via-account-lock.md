# # Description

Lab:  Authentication - **Username enumeration via account lock**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  05 October 2026</br>

# # What was vulnerable:

The application had a account lockout policy but the implementation of the mechanism left room for username enumeration and brute force of the password 

# # What I did:

1. Opened burp suite and tried to log in with random credentials.
2. Intercepted the request in burp suite and sent the request to burp intruder.
3. Using a cluster bomb attack, which iterates through every combination of the payloads assigned. We set position 1 to the username field (payload: the provided username list) and position 2 to the password field, using the 'Null payloads' generator set to repeat 5 times (this resends the same fixed password 5 times per username without changing it, accumulating failed attempts fast enough to trigger the lockout).
4. Running this attack we get the username that is valid as the account lockout policy only triggers for valid usernames. This works because the failed-attempt counter is almost certainly stored per-account on the server side. A nonexistent username has no account record to attach a counter to, so there's nothing to increment it can be hit indefinitely without ever locking, while a real account's counter climbs and eventually trips the lockout.
5. Using the valid username, we start a sniper attack with the payload position set to the password field and the list of passwords provided as the payload. We also modify the settings to display the error message with each enumeration.
6. After the completion of the attack we see that one password does not return any error message 
7. We wait for the account to be unlocked again and use the username and password to log into the application and solve the lab.

# # Why it worked:

This attack was done in two stages, the first one being username enumeration to get the correct username and the second being password brute force to get the password for the valid account.

To get the username we exploited a flaw in the account lockout policy where the lockout only triggers for valid usernames as the counter for the account lockout is stored server side and a non valid username will not have a record to attach a counter to, by using a single password and username for 4-5 times we observed that from the given list of usernames only one triggered the account lockout policy which was the valid username.

To brute force the password we observed that the valid password did not let us in the account due to the account lockout policy but it also did not show any error message confirming that the password was correct as every other password either showed “incorrect username or password” or “too many password attempts”. This happened because the application validates the password before checking whether the account is locked. For the correct password, credential validation succeeded first (producing no error) and only then did the lockout policy block the actual login.

# # Impact:

An attacker with valid credentials can access all functionality available to that account for administrator accounts this means full application control, user management, and potentially access to internal infrastructure and sensitive data.

# # Fix:

1. Limit the rate of requests to the login page - This will prevent a brute force attack as a threat actor won’t be able to send so many concurrent requests.
2. Implement the lockout policy to be universal even if the username is not valid.
3. Do not display different error messages for different errors.

# # Screenshot:
<img width="1507" height="217" alt="image" src="https://github.com/user-attachments/assets/d2baf19b-7bb9-4b5a-9ebb-25ce36490a80" />
