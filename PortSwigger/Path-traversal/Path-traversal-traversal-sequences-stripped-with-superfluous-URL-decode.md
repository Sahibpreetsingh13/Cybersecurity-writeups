# # description

Lab: Path traversal -   **traversal sequences stripped with superfluous URL-decode**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  30 June 2026</br>

# # What was vulnerable:

Path traversal via file parameter - the application used a black list filter to filter out the path traversal sequence from the decoded URL.

# # What I did:

The application firstly processed the URL provided to decode it and then used a black list filter to filter out the path traversal sequence. Used a double URL encoded path traversal sequence as the input `..%252f..%252f..%252fetc/passwd`  to gain access to the data.

# # Why it worked:

The application decoded the URL input once before passing it to the blacklist filter. The filter checked for `../` and `%2f` but not for `..%2f` — so by supplying `..%252f` the first decode converted `%25` to `%` producing `..%2f` which passed the filter unchallenged. The filesystem then decoded `%2f` as `/` completing the traversal sequence `../`. The vulnerability exists because decoding and sanitization happened in the wrong order — the application should sanitize after all decoding is complete, not between decode passes.

# # Impact:

An attacker can read sensitive files on the server including credentials, configuration files, and system files

# # Fix:

Validate and sanitize user supplied file paths, use a
whitelist of allowed files, and resolve the absolute path
before checking it stays within the intended directory

# # Screenshot:
<img width="1918" height="222" alt="image" src="https://github.com/user-attachments/assets/d8defc5e-e4e6-4657-b81f-dfb99abb4070" />
