# # Description

Lab: XSS- Reflected XSS into HTML context with most tags and attributes blocked</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed:  4 August 2026</br>

# # What was vulnerable:

The lab contained a reflected XSS vulnerability in the search functionality and also contains a WAF( web application firewall) but it was not implemented correctly leaving a few events and tags to still work.

# #Prerequisites:

1. Understanding of reflected XSS basics
2. Knowledge of WAF bypass techniques
3. Familiarity with Burp Intruder for fuzzing
4. Understanding of HTML event handlers
5. Understanding of iframe behavior and onload events

# # What I did:

1. Loaded the lab and used the search function with a alphanumeric test input to confirm that the input is reflected back.
2. Tried a basic tag and event `<img src=0 onerror=print(1)>` which returned an error saying this tag is blocked and the application also returned a 400 status code.
3. Opened burp suite and sent the test input to burp intruder with **§§** set between two brackets (<>) and the payload set being the list of possible tags (which were taken from the portswigger  cheat sheet for XSS). 
4. We were looking for a 200 status code indicating that the request went through and we found one for the `body` tag.
5. Now that we know the tag we just need to know the event that is not detected by the WAF. To do this we repeat the process with the burp intruder changing the input to `<body%20§§=1>`  and the payload set being replaced with the list of possible events (also taken from the portswigger cheat sheet for XSS) 
6. We found multiple event handlers that were not blocked by the WAF but all the event handler required user interaction but the lab explicitly mentioned  that no user interaction should be required to solve this lab.
7. With the information we found and the constrains of the lab we create an exploit using `<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?search=<body onresize=print()>" onload=this.style.width=”100px”>`  and deliver this exploit to the victim to complete the lab.

# # Why it worked:

The WAF implemented was not set up properly to block all events and tags leading to some events and tags still working. As the lab required no user interactions but all the events required user interaction to execute so we use `<iframe>`  in the exploit. The `iframe` loads the vulnerable page with the `<body onresize=print()>` payload injected via the search parameter. The iframe's own `onload` event fires immediately when it finishes loading, triggering `this.style.width='100px'` which resizes the iframe this resize cascades to the body element inside the iframe, firing `onresize` and executing `print()` without any victim interaction.

# # Impact:

An attacker could craft a URL containing this payload in the search parameter and send it to a victim. Since the script executes purely from reflected input with no server-side storage involved, this is a classic reflected XSS - the victim just needs to load the crafted link. The injected JavaScript would run in the origin of the vulnerable site, in the victim's session. Depending on what the site handles, this could enable session token/cookie theft, silent actions performed as the victim, or credential harvesting via injected fake login forms.

# # Fix:

1. Encode data on output -Encoding should be applied directly before user-controllable data is written to a page
2. Validate input on arrival -You should always validate input as strictly as possible at the point when it is first received from a user.
3. Don't rely solely on WAF blacklists for XSS prevention WAFs are bypassable through tag and event fuzzing. Implement server side output encoding as the primary defense, treating WAF as a secondary layer only.
4. The 400 status code indicated the WAF was actively blocking the request rather than just stripping the tag confirming a WAF was present and needed to be bypassed.

# # Screenshot:
<img width="1416" height="196" alt="image" src="https://github.com/user-attachments/assets/9156fd2d-5c41-464b-80e0-ed115fc8b3ed" />
