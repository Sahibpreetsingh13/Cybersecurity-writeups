# # Description

Lab:  File Upload - **Web shell upload via obfuscated file extension** </br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  16 August 2026</br>

# # What was vulnerable:

The image upload function for avatar selection is protected using a blacklist to validate the files uploaded by the users but the blacklist is bypassable using classic obfuscation techniques.The blacklist validation failed to strip or detect null bytes in filenames allowing the null byte to truncate the filename after validation, resulting in a different filename being stored than what was checked

# # Prerequisites

1. Basic understanding of PHP scripting
2. Understanding of web server file execution behavior
3. Knowledge of web root and directory structure
4. Familiarity with Burp Suite Repeater
5. Understanding of obfuscation techniques

# # What I did:

1. Started the lab, logged in with the given credentials and opened burp suite.
2. Uploaded an image file and observed the upload path `files/avatars/img.png` confirming the directory where uploaded files are stored and accessible, which would be the same path used to execute the malicious PHP file.
3. Made the script `<?php echo file_get_contents('/home/carlos/secret'); ?>` and named it exploit.php.
4. Tried uploading the exploit.php script and the file was blocked by the application.
5. Sent the upload POST request to the burp repeater and changed the file name to exploit.php.jpg and the file was accepted by the application. This indicates that the application just checks for .jpg / .png at the end of the file.
6. But the file is still not treated as a code to bypass this limit we add a null character `%00`  between the .php and .jpg so the file becomes `exploit.php%00jpg` 
7. Using burp suite repeater we send a request to `GET /files/avatars/exploit.php HTTP/1.1` to get the secret message and solve the lab.

# # Why it worked:

The image upload function was originally used to set an avatar for the profile and was secured using a black list but even the most secure black lists cant account for all possibilities so obfuscating the file extension bypasses the black list and allows the code to execute. To do this we used the filename `exploit.php%00.jpg`  the black list saw that the file was ending is jpg and passed it to the application but the server side code terminated the filename string at the null byte a behavior inherited from C-based string handling where null bytes signal end of string resulting in the file being saved as `exploit.php` while the blacklist had checked `exploit.php%00.jpg`When the PHP file was requested via GET the web server passed it to the PHP interpreter rather than serving it as a static file  because the upload directory was within the web root and the server was configured to execute PHP files. The script then ran with the web server's permissions, reading files accessible to that user.

# # Impact:

Beyond reading files, an attacker could upload a fully interactive web shell giving persistent command execution on the server enabling lateral movement, privilege escalation, data exfiltration, or using the server as a pivot point for attacking internal network resources.

# # Fix:

1. Validate file type server side — check the actual file content/magic bytes not just the extension or MIME type supplied by the user, which can be spoofed
2. Use a whitelist instead of a black list  — only permit image formats like jpeg, png, gif and reject everything else
3. Rename uploaded files — strip the original filename and extension, assign a random name with a safe extension to prevent execution
4. Store uploads outside the web root — files stored outside the publicly accessible directory cannot be executed even if malicious files are uploaded
5. Sanitize filenames by stripping null bytes and other special characters before processing — decode and normalize the filename completely before any validation or storage occurs

# # Screenshot:
<img width="1370" height="217" alt="image" src="https://github.com/user-attachments/assets/76099185-61ca-41fc-b90f-4dc4d24ea9f0" />
