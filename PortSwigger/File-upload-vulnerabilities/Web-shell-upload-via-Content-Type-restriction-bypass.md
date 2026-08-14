# # Description

Lab:  File Upload - **Web shell upload via Content-Type restriction bypass** </br>

Platform: PortSwigger Web Academy</br>

Difficulty: Apprentice</br>

Date Completed:  14 August 2026</br>

# # What was vulnerable:

The image upload function for avatar selection of the application did not properly validate the files uploaded by the users resulting in remote code execution using a php script. The application relied solely on the content-Type header to filter different file types and did not validate the files itself. 

# # Prerequisites

1. Basic understanding of PHP scripting
2. Understanding of web server file execution behavior
3. Knowledge of web root and directory structure
4. Familiarity with Burp Suite Repeater

# # What I did:

1. Started the lab, logged in with the given credentials and opened burp suite.
2. Uploaded an image file and observed the upload path `files/avatars/img.png`  confirming the directory where uploaded files are stored and accessible, which would be the same path used to execute the malicious PHP file
3. Made the script `<?php echo file_get_contents('/home/carlos/secret'); ?>` and named it exploit.php.
4. Tried uploading exploit.php to the image upload function and the request was blocked.
5. Intercepted the request using burp suite and sent it to burp repeater and changed the content-type of the request from `application/x-php` to `image/png` . and the request went through 
6. Using burp suite repeater we send a request to `GET /files/avatars/exploit.php HTTP/1.1`  to get the secret message and solve the lab.

# # Why it worked:

The image upload function was originally used to set an avatar for the profile but the only validation that the application performed was to check the content-type header to determine if the file uploaded was an image file or not. This type of validation was easily broken by intercepting the request and changing the content-type header to `image/png`  . Because of this lack of validation and firewall we were able to upload a PHP script to get access to sensitive data from the application. When the PHP file was requested via GET the web server passed it to the PHP interpreter rather than serving it as a static file  because the upload directory was within the web root and the server was configured to execute PHP files. The script then ran with the web server's permissions, reading files accessible to that user.

# # Impact:

Beyond reading files, an attacker could upload a fully interactive web shell giving persistent command execution on the server enabling lateral movement, privilege escalation, data exfiltration, or using the server as a pivot point for attacking internal network resources.

# # Fix:

1. Validate file type server side — check the actual file content/magic bytes not just the extension or MIME type supplied by the user, which can be spoofed

2. Maintain a whitelist of allowed file types — only permit image formats like jpeg, png, gif and reject everything else

3. Rename uploaded files — strip the original filename and extension, assign a random name with a safe extension to prevent execution

4. Store uploads outside the web root — files stored outside the publicly accessible directory cannot be executed even if malicious files are uploaded

5. Configure the server to not execute uploaded files — set the web server to serve files in the upload directory as static content only, never as executable scripts

# # Screenshot:
<img width="1633" height="208" alt="image" src="https://github.com/user-attachments/assets/0679409e-ab8e-4c0a-9f47-938247218f1b" />
