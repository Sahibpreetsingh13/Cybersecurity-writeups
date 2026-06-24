# # description

Lab: SSRF - **SSRF with blacklist-based input filter**  </br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  25 June 2026</br>

# # What was vulnerable:

The site implemented a blacklist based SSRF filter that blocked obvious loopback addresses and admin paths, but failed to account for alternative IP representations and double URL encoding as bypass techniques

# What I did:

1. Opened Burpsuite and checked for api calls made by the site.
2. Found a stock checker where an api call was made.
3. Intercepted the stock checker API request in Burp Suite, found a parameter containing the API URL, and replaced it with `http://127.0.0.1/admin` to make the server request its own admin panel
4. The site used some kind of filtering to block the loopback request. Tried alternative loopback representations — `127.1` worked because it is a valid shorthand for `127.0.0.1` that the blacklist didn't account for
5. The site had additional filtering to not grant access to the /admin sub domain , tried URL encoding the admin string but the filter still detected it.
6. finally double URL encoded the admin string to bypass the filter.
7. Gained access to the admin page and deleted the carlos user

# # Why it worked:

The site had a blacklist of URLs and sub-domains to prevent  a SSRF attack which can be bypassed by either using alternatives or by encoding the string parts to bypass the filter. Some sites are configured to trust any request coming from the home machine. This can be exploited by making it such that any api call made to the server can be altered with bypass to the filter in mind like 

`‘http%3A%2F%2F → http://
127.1 → loopback address
%2F%2561dmin → //admin (double encoded)`

This happens because the site treats the request from home machine different from other requests.

# # Impact:

An attacker can gain unauthorized access to the site and make changes like a higher privileged user

# # Fix:

Avoid blacklist based filtering for SSRF prevention entirely blacklists are inherently bypassable through alternative IP representations (127.1, 0x7f000001, 2130706433), DNS rebinding and encoding techniques like URL encoding or double encoding.

Instead implement:

- Whitelist of explicitly allowed domains and IPs only
- Server side URL parsing that normalizes URLs before
validation — decode all encoding before checking against rules
- Disable unnecessary outbound server requests entirely
- Use an allowlist for internal service communication rather
than relying on network level trust of loopback addresses

# # Screenshot:
# # description

Lab: SSRF - **SSRF with blacklist-based input filter**  </br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  25 June 2026</br>

# # What was vulnerable:

The site implemented a blacklist based SSRF filter that blocked obvious loopback addresses and admin paths, but failed to account for alternative IP representations and double URL encoding as bypass techniques

# What I did:

1. Opened Burpsuite and checked for api calls made by the site.
2. Found a stock checker where an api call was made.
3. Intercepted the stock checker API request in Burp Suite, found a parameter containing the API URL, and replaced it with `http://127.0.0.1/admin` to make the server request its own admin panel
4. The site used some kind of filtering to block the loopback request. Tried alternative loopback representations — `127.1` worked because it is a valid shorthand for `127.0.0.1` that the blacklist didn't account for
5. The site had additional filtering to not grant access to the /admin sub domain , tried URL encoding the admin string but the filter still detected it.
6. finally double URL encoded the admin string to bypass the filter.
7. Gained access to the admin page and deleted the carlos user

# # Why it worked:

The site had a blacklist of URLs and sub-domains to prevent  a SSRF attack which can be bypassed by either using alternatives or by encoding the string parts to bypass the filter. Some sites are configured to trust any request coming from the home machine. This can be exploited by making it such that any api call made to the server can be altered with bypass to the filter in mind like 

`‘http%3A%2F%2F → http://
127.1 → loopback address
%2F%2561dmin → //admin (double encoded)`

This happens because the site treats the request from home machine different from other requests.

# # Impact:

An attacker can gain unauthorized access to the site and make changes like a higher privileged user

# # Fix:

Avoid blacklist based filtering for SSRF prevention entirely blacklists are inherently bypassable through alternative IP representations (127.1, 0x7f000001, 2130706433), DNS rebinding and encoding techniques like URL encoding or double encoding.

Instead implement:

- Whitelist of explicitly allowed domains and IPs only
- Server side URL parsing that normalizes URLs before
validation
- decode all encoding before checking against rules
- Disable unnecessary outbound server requests entirely
- Use an allowlist for internal service communication rather
than relying on network level trust of loopback addresses

# # Screenshot:
<img width="1917" height="438" alt="image" src="https://github.com/user-attachments/assets/a86adf28-8b6d-4917-b1f7-7d2a2bc24968" />
