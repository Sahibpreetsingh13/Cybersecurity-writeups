# # Description

Lab:  File Upload - **Remote code execution via web shell upload** </br>

Platform: PortSwigger Web Academy</br>

Difficulty: Apprentice</br>

Date Completed:  8 August 2026</br>

# # What was vulnerable:

The image upload function for avatar selection of the application did not properly validate the files uploaded by the users resulting in remote code execution using a php script.

# # Prerequisites

1. Basic understanding of PHP scripting
2. Understanding of web server file execution behavior
3. Knowledge of web root and directory structure
4. Familiarity with Burp Suite Repeater

# # What I did:

1. Started the lab,logged in with the given credentials and opened burp suite.
2. Made the script `<?php echo file_get_contents('/home/carlos/secret'); ?>`  and named it exploit.php.
3. Uploaded exploit.php to the image upload feature and Found the upload request in HTTP history confirming the file was stored at `/files/avatars/exploit.php` this path revealed both the upload directory structure and that the original filename was preserved.
4. Using burp suite repeater we send a request to `GET /files/avatars/exploit.php HTTP/1.1`  to get the secret message and solve the lab.

# # Why it worked:

The image upload function was originally used to set an avatar for the profile but it did not validate the files uploaded by the users neither did it have a blacklist/firewall to stop the upload of file types that were not image files. Because of this lack of validation and firewall we were able to upload a PHP script to get access to sensitive data from the application. When the PHP file was requested via GET the web server passed it to the PHP interpreter rather than serving it as a static file  because the upload directory was within the web root and the server was configured to execute PHP files. The script then ran with the web server's permissions, reading files accessible to that user

# # Impact:

Beyond reading files, an attacker could upload a fully interactive web shell giving persistent command execution on the server enabling lateral movement, privilege escalation, data exfiltration, or using the server as a pivot point for attacking internal network resources.

# # Fix:

1. Validate file type server side — check the actual file content/magic bytes not just the extension or MIME type supplied by the user, which can be spoofed

2. Maintain a whitelist of allowed file types — only permit image formats like jpeg, png, gif and reject everything else

3. Rename uploaded files — strip the original filename and extension, assign a random name with a safe extension to prevent execution

4. Store uploads outside the web root — files stored outside the publicly accessible directory cannot be executed even if malicious files are uploaded

5. Configure the server to not execute uploaded files — set the web server to serve files in the upload directory as static content only, never as executable scripts

# # Screenshot:
<img width="1467" height="200" alt="image" src="https://github.com/user-attachments/assets/a777154c-b2c0-44dc-ae6c-5c6ed8d9fbbb" />
