# # Description

Lab: XSS-Stored XSS into HTML context with nothing encoded</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Apprentice </br>

Date Completed:  28 July 2026</br>

# # What was vulnerable:

The application was vulnerable to a Stored cross site scripting (XSS) attack through the comment functionality of the application.

# # What I did:

Identified the comment functionality as a potential injection point. Entered `<script>alert(1)</script>` as the comment and observed that the comment was stored directly as HTML without any encoding and the next time you visit the post with the malicious comment the script is executed, triggering the alert popup.

# # Why it worked:

The application had no form of input sanitization for the comment functionality so any code we put into the comment box the application ran as the actual code rather than just being the input so when it encountered <script> it treated it as valid JavaScript. This is Stored XSS  is which  the application receives data from an untrusted source and includes that data within its later HTTP responses in an unsafe way. The next time that HTTP response is called the script is executed. Unlike reflected XSS which requires tricking a victim into clicking a malicious link, stored XSS executes automatically for every user who visits the page no social engineering required, making it significantly more impactful

# # Impact:

An attacker who can execute arbitrary JavaScript in a victim's browser can perform any action the victim can stealing session cookies, making requests on their behalf, capturing keystrokes, redirecting to phishing pages, or defacing the page content.

# # Fix:

1. Encode data on output -Encoding should be applied directly before user-controllable data is written to a page
2. Validate input on arrival -You should always validate input as strictly as possible at the point when it is first received from a user.
3. Implement Content Security Policy (CSP) — restricts which scripts can execute on the page, limiting the impact of any XSS that does get through.

# # Screenshot:
<img width="1860" height="202" alt="image" src="https://github.com/user-attachments/assets/2365459e-2301-4279-93e8-8718613b0041" />
