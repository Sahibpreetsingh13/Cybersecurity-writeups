# # description

Lab: Path traversal - Simple case </br>

Platform: PortSwigger Web Academy</br>

Difficulty: Apprentice</br>

Date Completed:  29 June 2026</br>

# # What was vulnerable:

Path traversal via file parameter 

# # What I did:

Intercepted image loading request in Burp Suite, found a
filename parameter, replaced the value with ../../../etc/passwd
to traverse outside the web root and access sensitive system files

# # Why it worked:

The application passed user input directly to filesystem
operations without sanitizing ../ sequences, allowing
navigation outside the intended directory

# # Impact:

An attacker can read sensitive files on the server including
credentials, configuration files, and system files

# # Fix:

Validate and sanitize user supplied file paths, use a
whitelist of allowed files, and resolve the absolute path
before checking it stays within the intended directory

# # Screenshot:
<img width="1882" height="212" alt="image" src="https://github.com/user-attachments/assets/05062b90-603b-40b3-bdf8-9cb5e364c91f" />
