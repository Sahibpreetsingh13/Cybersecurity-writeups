# # Description

Lab:  File Upload - Remote code execution via polyglot web shell upload </br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  18 August 2026</br>

# # What was vulnerable:

The image upload function for avatar selection does check the contents of the file to verify if it is a image or not but it can be bypassed by injecting a malicious code into a image file using a tool like exiftool.

# # Prerequisites

1. Basic understanding of PHP scripting
2. Understanding of web server file execution behavior
3. Knowledge of web root and directory structure
4. Familiarity with Burp Suite Repeater and exiftool

# # What I did:

1. Started the lab, logged in with the given credentials and opened burp suite.
2. Uploaded an image file and observed the upload path `files/avatars/img.png` confirming the directory where uploaded files are stored and accessible, which would be the same path used to execute the malicious PHP file.
3. Made the script `<?php echo file_get_contents('/home/carlos/secret'); ?>` and named it exploit.php.
4. Tried uploading the exploit.php script and the file was blocked by the application.
5. Using exiftool added the same script in exploit.php to the comments of the  img.png file using the command: `exiftool -Comment="<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>" img.png -o img.php`  and upload it.
6. Using burp suite repeater we send a request to `GET /files/avatars/img.php HTTP/1.1` to get the secret message and solve the lab.

# # Why it worked:

The image upload function was originally used to set an avatar for the profile and the contents of the files uploaded by the user were checked by the application to confirm that the file was indeed an image but this check was bypassed by using exiftool to insert the malicious code into the comment of a regular image file and save it as a PHP file since the content check verified the file contained valid image data which it did since the base file was a real PNG  but failed to scan the metadata and comment fields where the PHP payload was embedded. The extension check was either absent or insufficient, allowing the .php extension to be saved and executed the PHP file is uploaded to the server and  when the PHP file was requested via GET the web server passed it to the PHP interpreter rather than serving it as a static file  because the upload directory was within the web root and the server was configured to execute PHP files. The script then ran with the web server's permissions, reading files accessible to that user. This works because most image validation libraries check the file header and structure  a real PNG header satisfies the check without examining every byte of metadata where executable code can hide.

# # Impact:

Beyond reading files, an attacker could upload a fully interactive web shell giving persistent command execution on the server enabling lateral movement, privilege escalation, data exfiltration, or using the server as a pivot point for attacking internal network resources.

# # Fix:

1. Validate file type server side — check the actual file content/magic bytes not just the extension or MIME type supplied by the user, which can be spoofed
2. Use a whitelist instead of a black list  — only permit image formats like jpeg, png, gif and reject everything else
3. Rename uploaded files — strip the original filename and extension, assign a random name with a safe extension to prevent execution
4. Store uploads outside the web root — files stored outside the publicly accessible directory cannot be executed even if malicious files are uploaded
5. Strip all metadata from uploaded files using a library like ImageMagick before storing — this removes embedded payloads from EXIF comments, ICC profiles, and other metadata fields regardless of file type validation results

# # Screenshot:
<img width="1453" height="208" alt="image" src="https://github.com/user-attachments/assets/bf853b28-7504-49c5-944a-d23af172991b" />
