# # description

Lab: Path traversal -   **validation of file extension with null byte bypass**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  30 June 2026</br>

# # What was vulnerable:

Path traversal via file parameter - application validated file extension after user input but before path resolution, allowing null byte injection to truncate the actual path the OS processed

# # What I did:

The application validated the file extension by checking if the file ends with a .png and this check takes place before resolving the path of the URL. So used `../../../etc/passwd%00.png`  as the input. The validation clears it as it has .png at the end but when the OS resolves this it stops once it encounters the null byte.

# # Why it worked:

By using `../../../etc/passwd%00.png`  the %00 is a URL encoded version of a null byte as the path is not yet resolved it validates the URL as it has .png at the end but when the OS tries to resolve the URL it encounters the null byte and stops continuing after that so it sees `../../../etc/passwd` and access the location in the path 

# # Impact:

An attacker can read sensitive files on the server including credentials, configuration files, and system files

# # Fix:

Firstly validate the input after resolving the path and secondly implement a white list filter to avoid characters like %00 as the normal use case would never need a null byte

# # Screenshot:
<img width="1817" height="215" alt="image" src="https://github.com/user-attachments/assets/ac8e68d8-1e90-4584-bc47-73e0fcb508d9" />
