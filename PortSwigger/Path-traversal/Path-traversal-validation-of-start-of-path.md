# # description

Lab: Path traversal -   **validation of start of path**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  30 June 2026</br>

# # What was vulnerable:

Path traversal via file parameter - the application only validated only the start of the path 

# # What I did:

The application validated only the start of the path before resolving the path traversal sequence. Used an input like `/var/www/images/../../../etc/passwd`  which passes the validation as it starts with `/var/www/images/`  but after resolution of the path the traversal sequence is executed 

# # Why it worked:

The application validated the path before resolving the path which leads to checking the input `/var/www/images/../../../etc/passwd`  first and confirming that the start of the path is the same as intended but after the validation is complete the os resolves the `/../../../` taking it back to the root directory and accesses `etc/passwd`

# # Impact:

An attacker can read sensitive files on the server including credentials, configuration files, and system files

# # Fix:

Resolve the absolute path using a function like `realpath()` before validation, then verify the resolved path still starts with the intended base directory. This ensures `../` sequences are fully resolved before the check runs, preventing the mismatch between what validation sees and what the OS actually accesses.

# # Screenshot:
<img width="1898" height="212" alt="image" src="https://github.com/user-attachments/assets/b16f017b-c1f7-457b-a745-0dd9d5e277d3" />
