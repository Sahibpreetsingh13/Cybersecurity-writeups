# # description

Lab: Path traversal - absolute path bypass </br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  29 June 2026</br>

# # What was vulnerable:

Path traversal via file parameter 

# # What I did:

Intercepted image loading request in Burp Suite, found a
filename parameter, replaced the value with  /etc/passwd making the path absolute.

# # Why it worked:

The input `/etc/passwd` is an absolute path starting with `/`. When concatenated with the base directory the OS encountered a second `/` and reset to root, discarding `/var/www/images/` entirely and resolving directly to `/etc/passwd`

# # Impact:

An attacker can read sensitive files on the server including credentials, configuration files, and system files

# # Fix:

Validate and sanitize user supplied file paths, use a
whitelist of allowed files, and resolve the absolute path
before checking it stays within the intended directory

# # Screenshot:
<img width="1882" height="202" alt="image" src="https://github.com/user-attachments/assets/9168f46b-13f4-431e-ab95-eabadfe4f0bb" />
