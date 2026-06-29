# # description

Lab: SSRF - **SSRF with filter bypass via open redirection vulnerability** </br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  29 June 2026</br>

# # What was vulnerable:

The site was vulnerable to a Server side request forgery where  an attacker can imitate a request to look like its coming from the server.

# # What I did:

1. Opened Burpsuite and checked for api calls made by the site.
2. Found a stock checker where an api call was made.
3. Intercepted the stock checker API request in Burp Suite, found a parameter containing the API URL.
4. Tried to change the API request to go to `http://192.168.0.12:8080/admin`  which was provided beforehand as the admin URL but the request was blocked.
5. Tried various alternatives like encoding the URL and double encoding the URL to bypass the filter
6. Once it was certain that it was a whitelist filter instead of a blacklist filter. Tried looking for other methods on the site found that when ‘next product’ is clicked it performs a redirection 
7. Used this to craft an exploit that used this property of the site and redirected to the admin panel by sending `/product/nextProduct?path=http://192.168.0.12:8080/admin`  as the API reqeuest.
8. Gained access to the admin page and deleted the carlos user

# # Why it worked:

The application implemented a whitelist filter on the stock checker's API parameter, blocking direct requests to internal addresses like `http://192.168.0.12:8080/admin`. However, the same application exposed an open redirect via the `/product/nextProduct?path=` parameter, which would redirect to any arbitrary URL passed to it — including internal ones. Because the open redirect endpoint resided on the whitelisted domain itself, the server accepted it as a trusted URL. When the forged request was processed, the server followed the redirect internally and fetched the admin panel on behalf of the attacker. Since internal services often trust requests originating from within the same server without requiring authentication, the admin panel was returned in full — allowing the attacker to perform privileged actions such as deleting users.

In short: the whitelist filter checked *where the request started*, but not *where it ultimately ended up*.

# # Impact:

The admin panel was accessible without authentication.

# # Fix:

1. **Restrict open redirects** — The `/product/nextProduct?path=` parameter should only accept relative paths, not absolute URLs. This removes the redirect gadget used to bypass the whitelist.
2. **Validate the final destination** — The SSRF filter should follow and validate any redirects before making a request. A URL that starts on a trusted domain but redirects to an internal address should be blocked.
3. **Block internal IP ranges** — Outbound server-side requests should be denied if they resolve to private IP ranges (10.x.x.x, 172.16.x.x, 192.168.x.x) or loopback addresses, regardless of how the request was constructed.
4. **Enforce authentication on internal services** — Internal admin panels should never grant access based solely on the request coming from localhost or an internal IP. Proper authentication must be enforced at the service level.

# # Screenshot:
<img width="1892" height="363" alt="image" src="https://github.com/user-attachments/assets/6699d25c-488d-4b7c-b281-12c6f72ac925" />
