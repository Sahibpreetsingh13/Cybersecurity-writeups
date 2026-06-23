# Description 
Lab: Web cache deception - path mapping </br>
Platform: PortSwigger Web Academy</br>
Difficulty: Apprentice</br>
Date Completed:  2 June 2026</br>

# What was vulnerable:
the cache server and the origin server mapping the URL of a resource differently

# What I did:
1. Opened Burpsuite and logged into the account with the given credentials 
2. Sent the request to repeater and tried adding a static sub domain to the URL
3. the cache server constructed a cache key based on the `.js` file extension, classifying the response as a static resource eligible for caching, while the origin server ignored the extension entirely and returned the dynamic account page
4. crafted an exploit that served the infected URL to the victim 
5. once the victim opens the infected URL and it gets cached we do the same and get access to sensitive information in the account

# Why it worked:
the origin server used REST styled mapping and ignored the static sub domain added to the request while the cache server using a traditional URL mapping assumes the request to be made to a static file with the rest of the URL being the path to the file and caches it as it is configured to cache static files 

# Impact:
An attacker can gain access to a victim’s account and/or sensitive information

# Fix:
Making sure that both the origin server and the cache server use the same kind of URL mapping 

# Screenshot: 
<img width="1817" height="935" alt="image" src="https://github.com/user-attachments/assets/693b81bc-33f2-4a01-8e8b-d0c10cf5833c" />
