# # description

Lab: SSRF - Basic SSRF against the local server  </br>

Platform: PortSwigger Web Academy</br>

Difficulty: Apprentice</br>

Date Completed:  24 June 2026</br>

# # What was vulnerable:

The site was vulnerable to a Server side request forgery where  an attacker can imitate a request coming from the server.

# What I did:

1. Opened Burpsuite and checked for api calls made by the site.
2. Found a stock checker where an api call was made.
3. Intercepted the stock checker API request in Burp Suite, found a parameter containing the API URL, and replaced it with `http://127.0.0.1/admin` to make the server request its own admin panel
4. Gained access to the admin page and deleted the carlos user

# # Why it worked:

Some sites are configured to trust any request coming from the home machine. This can be exploited by making it such that any api call made to the server can be altered with a loopback address and a command like `://127.0.0.1/admin`. This happens because the site treats the request from home machine different from other requests.

# # Impact:

An attacker can gain unauthorized access to the site and make changes like a higher privileged user

# # Fix:

Implement a whitelist of allowed URLs the server can make requests to, blocking requests to internal addresses like 127.0.0.1 and internal network ranges. Avoid passing user supplied URLs directly to server side request functions.

# # Screenshot:
<img width="1387" height="417" alt="image" src="https://github.com/user-attachments/assets/4276418b-7cf7-46da-9442-03e9c775c3cf" />
