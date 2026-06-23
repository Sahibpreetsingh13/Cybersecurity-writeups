Lab: Web cache deception - path delimiters 
Platform: PortSwigger Web Academy
Difficulty: Practitioner 
Date Completed:  02 June 2026

What was vulnerable:
The delimiters recognized by the origin server were not recognized by the cache server 

What I did:

1. Opened Burpsuite and logged into the account with the given credentials
2. Sent the request to repeater and systematically tested common delimiters — `;`, `?`, `#` — to identify which ones the origin server recognized while the cache server ignored
3. Appended `.js` after the delimiter to make the URL appear as a static JavaScript file to the cache server
4. The cache server constructed a cache key based on the `.js` file extension, classifying the response as a static resource eligible for caching, while the origin server ignored the argument after the delimiter and returned the dynamic account page
5. crafted an exploit that served the infected URL to the victim
6. once the victim opens the infected URL and it gets cached we do the same and get access to sensitive information in the account

Why it worked:
the origin server uses a framework with certain delimiters like ; in java spring framework to add parameters known as matrix variables. so they truncate the path after the delimiter while the cache server which is not using the framework treats the URL after the delimiter as the actual address. With the static file extension at the end and caches the response.

Impact:
An attacker can gain access to a victim’s account and/or sensitive information

Fix:
Ensure cache and origin servers normalize URLs consistently before constructing cache keys. Configure the cache to strip or ignore path parameters that the origin framework processes, or use a WAF rule to block requests with unexpected delimiters in static file paths.

Screenshot:
!image.png
