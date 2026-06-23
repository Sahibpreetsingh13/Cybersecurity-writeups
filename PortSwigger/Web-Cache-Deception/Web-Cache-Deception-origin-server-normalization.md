# Description 
Lab: Web cache deception - path delimiters </br>
Platform: PortSwigger Web Academy</br>
Difficulty: Practitioner </br>
Date Completed:  03 June 2026</br>

# What was vulnerable:
Discrepancy in how the cache and origin server normalized the URL

# What I did:
1. Opened Burpsuite and logged into the account with the given credentials
2. Tested how the origin server normalized slash characters and replaces dot segments. by sending request like `aaa/..%2fmy-account`
3. Once confirmed that the URL still leads to `my-account` we test the normalization of the cache server and found out that the way URLs are normalized are different in the cache server and the origin server
4. So we found the static directories used in the site that are cached like /assests,  /resources etc
5. Crafted a URL like `resources/..%2fmy-account` — the cache server saw this as a request within the static /resources directory and cached it, while the origin server decoded the path traversal and served the dynamic account page
6. once the victim opens the infected URL and it gets cached we do the same and get access to sensitive information in the account

# Why it worked:
The origin server decoded `%2f` as a slash and resolved `..` as a path traversal, ultimately serving `/my-account`. The cache server however did not decode or resolve these characters, treating the full string as a path within the static `/resources` directory. Since the cache was configured to store all responses from static directories, it cached the sensitive account page under what appeared to be a static file path

#Impact:
An attacker can gain access to a victim’s account and/or sensitive information

#Fix:
Ensure cache and origin servers normalize URLs consistently before constructing cache keys. and the rules of normalization are applied to both the origin server and the cache server where both the servers resolve slash characters and replace the dot segments.

# Screenshot:
<img width="1527" height="722" alt="image" src="https://github.com/user-attachments/assets/30ec412e-38f5-472d-9cea-49887b3055f6" />

