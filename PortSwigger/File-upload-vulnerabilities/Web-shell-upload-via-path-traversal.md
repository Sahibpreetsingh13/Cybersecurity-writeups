# # Description

Lab:  File Upload - **Web shell upload via path traversal** </br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  16 August 2026</br>

# # What was vulnerable:

The image upload function for avatar selection did not validate the files uploaded by the user but the server was configured to not execute code from user supplied files and treat them as plain text but that was bypassable using path traversal

# # Prerequisites

1. Basic understanding of PHP scripting
2. Understanding of web server file execution behavior
3. Knowledge of web root and directory structure
4. Familiarity with Burp Suite Repeater
5. Understanding of path traversal attacks 

# # What I did:

1. Started the lab, logged in with the given credentials and opened burp suite.
2. Uploaded an image file and observed the upload path `files/avatars/img.png`  confirming the directory where uploaded files are stored and accessible, which would be the same path used to execute the malicious PHP file.
3. Made the script `<?php echo file_get_contents('/home/carlos/secret'); ?>` and named it exploit.php.
4. Uploaded the exploit.php script and the file was successfully uploaded but when we open the upload path `files/avatars/exploit.php` . The code is treated as a plain text file rather than an executable code
5. To bypass this we open the burp suite repeater with the upload POST request and modify the request from `filename="exploit.php"`  to `filename="../exploit.php"`  and observed that the uploaded file did not show the change this suggests that the server is stripping the directory traversal sequence from the files 
6. So we modify the request to `filename="..%2fexploit.php"`  to bypass the normalization and get `../exploit.php`  uploaded to the server.
7. Sent GET request to `/files/exploit.php` — one directory above avatars where the server executed PHP files rather than serving them as static content.

# # Why it worked:

The image upload function was originally used to set an avatar for the profile but any file type could be uploaded to the server. To prevent malicious code execution the server was configured to not execute user supplied files as code but the server was configured to only do this in the `files/avatars`  so by intercepting the file upload request and changing the filename from `exploit.php` to `..%2fexploit.php`.  Where %2f is the URL encoded version of ‘/’.  Executing a path traversal attack and moving the uploaded file from `files/avatars/exploit.php` to`files/exploit.php` one directory above the restricted avatars folder, in a location where the server was configured to execute PHP allowing the PHP file requested via GET to be passed  to the PHP interpreter rather than serving it as a static file  because the upload directory was within the web root and the server was configured to execute PHP files. The script then ran with the web server's permissions, reading files accessible to that user.

# # Impact:

Beyond reading files, an attacker could upload a fully interactive web shell giving persistent command execution on the server enabling lateral movement, privilege escalation, data exfiltration, or using the server as a pivot point for attacking internal network resources.

# # Fix:

1. Validate file type server side — check the actual file content/magic bytes not just the extension or MIME type supplied by the user, which can be spoofed
2. Maintain a whitelist of allowed file types — only permit image formats like jpeg, png, gif and reject everything else
3. Rename uploaded files — strip the original filename and extension, assign a random name with a safe extension to prevent execution
4. Store uploads outside the web root — files stored outside the publicly accessible directory cannot be executed even if malicious files are uploaded
5. Sanitize filenames server side — decode and normalize filenames before processing, strip all directory traversal sequences including URL encoded variants like `%2f` before saving the file

# # Screenshot:
<img width="1453" height="208" alt="image" src="https://github.com/user-attachments/assets/f5a69bde-8943-42c5-90c4-a598b95c3b51" />
