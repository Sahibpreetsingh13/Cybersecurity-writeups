# # Description

Lab:  File Upload - **Web shell upload via extension blacklist bypass** </br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  16 August 2026</br>

# # What was vulnerable:

The image upload function for avatar selection is protected using a blacklist to validate the files uploaded by the users but the black list contains a fundamental flaw in its configuration. The blacklist failed to include server configuration files like .htaccess allowing an attacker to reconfigure PHP execution rules for specific directories and bypass the extension blacklist entirely.

# # Prerequisites

1. Basic understanding of PHP scripting
2. Understanding of web server file execution behavior
3. Knowledge of web root and directory structure
4. Familiarity with Burp Suite Repeater
5. knowledge of server configurations and directory-specific configuration in servers

# # What I did:

1. Started the lab, logged in with the given credentials and opened burp suite.
2. Uploaded an image file and observed the upload path `files/avatars/img.png` confirming the directory where uploaded files are stored and accessible, which would be the same path used to execute the malicious PHP file.
3. Made the script `<?php echo file_get_contents('/home/carlos/secret'); ?>` and named it exploit.php.
4. Uploaded the exploit.php script and the file was blocked by the server 
5. Sent the upload POST request to the burp repeater and changed the file name parameter to .htaccess and the content-type parameter to text/plain since .htaccess is a text configuration file matching the expected MIME type for the server to accept itand the file content to  `AddType application/x-httpd-php .l33t`  and click on send request and observe that the request went through
6. now we click on the back button in the burp repeater to return to the original request and change filename from exploit.php to exploit.l33t and the content-type to `application/x-httpd-php`  and make the request, we observe that the request went through 
7. Using burp suite repeater we send a request to `GET /files/avatars/exploit.l33t HTTP/1.1` to get the secret message and solve the lab.

# # Why it worked:

The image upload function was originally used to set an avatar for the profile and was secured using a black list but the black list was misconfigured and allowed the user to upload a .htaccess file which is used in the apache servers to make directory-specific configurations and we upload the configuration `AddType application/x-httpd-php .l33t`  which basically tells the server to treat the .l33t file extension as php code and as the blacklist in the server did not have a rule for .l33t file extension it was easily uploaded and we could access it in the `files/avatars/exploit.l33t`   directory the .l33t file requested via GET  was passed  to the PHP interpreter rather than serving it as a static file because the .htaccess file we uploaded reconfigured that specific directory to treat .l33t files as PHP, overriding the default server behavior for that directory only. The script then ran with the web server's permissions, reading files accessible to that user.

# # Impact:

Beyond reading files, an attacker could upload a fully interactive web shell giving persistent command execution on the server enabling lateral movement, privilege escalation, data exfiltration, or using the server as a pivot point for attacking internal network resources.

# # Fix:

1. Validate file type server side — check the actual file content/magic bytes not just the extension or MIME type supplied by the user, which can be spoofed
2. Use a whitelist instead of a black list  — only permit image formats like jpeg, png, gif and reject everything else
3. Rename uploaded files — strip the original filename and extension, assign a random name with a safe extension to prevent execution
4. Store uploads outside the web root — files stored outside the publicly accessible directory cannot be executed even if malicious files are uploaded
5. Explicitly block server configuration files — add .htaccess, .htpasswd, and web.config to the blacklist, or better yet switch to a whitelist so these are blocked by default without needing to enumerate every dangerous file type

# # Screenshot:
<img width="1460" height="201" alt="image" src="https://github.com/user-attachments/assets/94068a9e-246d-4e9b-ac3c-ba8be6f78c23" />
