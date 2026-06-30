# # description

Lab: Path traversal -  **traversal sequences stripped non-recursively**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  30 June 2026</br>

# # What was vulnerable:

Path traversal via file parameter — application used a non-recursive blacklist filter that stripped `../` sequences only once, allowing nested payloads to bypass the sanitization

# # What I did:

The application filtered `../` sequences so standard path traversal was blocked. Used nested payload `....//....//....//etc/passwd` — when the filter stripped the inner `../` from `....//` it left behind `../` completing the traversal sequence

# # Why it worked:

The filter stripped `../` from user input but only made a single pass without checking if the removal created new traversal sequences. By nesting the payload as `....//` — when the filter removed the inner `../` it left behind `../` which was then used for traversal. This is a non-recursive filter weakness.

# # Impact:

An attacker can read sensitive files on the server including credentials, configuration files, and system files

# # Fix:

Validate and sanitize user supplied file paths, use a
whitelist of allowed files, and resolve the absolute path
before checking it stays within the intended directory

# # Screenshot:
<img width="1913" height="212" alt="image" src="https://github.com/user-attachments/assets/9a49d1cd-d7dc-464c-a588-9f3e712b7260" />
