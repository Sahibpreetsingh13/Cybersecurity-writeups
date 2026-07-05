# # description

Lab: Old Sessions </br>

Platform: PicoCTF (Now cylabs)</br>

Difficulty: easy</br>

Date Completed:  3 july 2026</br>

# # What was vulnerable:

Publicly accessible /sessions endpoint exposed valid session IDs of all users including admin, combined with non-expiring session tokens allowing indefinite reuse

# # What I did:

1. Registered an account and logged in
2. Intercepted requests in Burp Suite, identified session ID
being passed as a parameter on return visits
3. Used wfuzz to fuzz for hidden directories, discovered
/sessions endpoint
4. Accessed /sessions which exposed session IDs of all users
including admin
5. Sent a request with the admin session ID and gained
admin access, retrieved the flag

# # Why it worked:

1. The /sessions stored every session id logged into through the device 
2. The session key/id never expires resulting in infinite usage of the session id 
3. The /sessions directory was public but should have been hidden or only visible as the admin 

# # Impact:

An attacker can read sensitive data from any user who has once logged into their account using the same device as the attacker

# # Fix:

1. Secure the directory and keep it hidden or after a admin login 
2. Set up a system where session ids can not be used infinitely and expire after a certain time or once the person logs out.
