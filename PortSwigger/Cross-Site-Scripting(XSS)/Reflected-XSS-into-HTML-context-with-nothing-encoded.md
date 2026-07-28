# # Description

Lab: XSS-Reflected XSS into HTML context with nothing encoded</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Apprentice </br>

Date Completed:  28 July 2026</br>

# # What was vulnerable:

The application was vulnerable to a reflected cross site scripting (XSS) attack through the search functionality of the application. 

# # What I did:

Identified the search functionality as a potential injection point. Entered `<script>alert(1)</script>` as the search query and observed the application reflected the input directly into the HTML response without encoding, executing the script and triggering the alert popup.

# # Why it worked:

The application had no form of input sanitization for the search functionality so any code we put into the search box the application ran as the actual code rather than just being the input so when it encountered <script> it treated it as valid JavaScript. This is reflected XSS  the payload travels in the request and is immediately reflected back in the response, executing in the victim's browser. Unlike stored XSS the payload isn't saved in the database the victim must be tricked into clicking a crafted link containing the payload.

# # Impact:

An attacker who can execute arbitrary JavaScript in a victim's browser can perform any action the victim can stealing session cookies, making requests on their behalf, capturing keystrokes, redirecting to phishing pages, or defacing the page content.

# # Fix:

1. Encode data on output -Encoding should be applied directly before user-controllable data is written to a page
2. Validate input on arrival - You should always validate input as strictly as possible at the point when it is first received from a user.

# # Screenshot:
<img width="1857" height="207" alt="image" src="https://github.com/user-attachments/assets/af18f4c7-7ece-4e0a-887d-d5d2264c0f22" />
