# # Description

Lab: XSS-  **DOM XSS in jQuery anchor `href` attribute sink using `location.search` source**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Apprentice </br>

Date Completed:  1 August 2026</br>

# # What was vulnerable:

The feedback page uses a jQuery script that reads the `returnPath` parameter directly from `location.search` and assigns it to the `href` attribute of the back button link without validation allowing the `javascript:` protocol to be injected as a navigable URL.

# #Prerequisites

1. Understanding of DOM XSS sources and sinks
2. Understanding the  `href`  attribute 
3. Knowledge of HTML event handlers as XSS vectors
4. Difference between DOM XSS and reflected/stored XSS
5. Understanding the jQuery library of JavaScript.

# # What I did:

1. Loaded the lab and used the submit feedback feature, the application used a return path used in the URL of the feedback feature to redirect users after they submit the feedback or click the back button. Used DevTools in the browser to check the source and sink used.
2. After finding that the source was `location.search`  and the sink was `href` figured changing the query in the URL can result in a DOM-XSS vulnerability.
3. Modified the query to change the return path to `javascript:alert(document.cookie)` .
4. Clicked on the back button to trigger the script and run the code to complete the lab.

# # Why it worked:

The sink used in the DOM was `href`  which takes in a URL to make the return path when the back button is clicked or the feedback form is submitted. The `JavaScript:` is treated as a valid protocol like ftp, https so when you click the back button the JavaScript is executed. Also the source was `location.search`  which tell us change the return path directly from the URL. The vulnerability exists in jQuery's `.attr()` method which sets the `href`  value directly from user controlled input without sanitizing the `javascript:` protocol a dangerous pattern common in older jQuery based applications.Unlike event handler based XSS like `onerror` which fires automatically, this payload requires the victim to click the back button  making it slightly harder to exploit but still very viable through social engineering.

# # Impact:

An attacker could craft a malicious URL containing this payload and send it to a victim. Since the XSS fires purely from the URL with no server-side storage involved, this is a classic DOM-based reflected XSS victims just need to click the link. Once triggered, the attacker's script runs in the victim's browser session, in the origin of the vulnerable site. Depending on what the site handles, this could enable session token/cookie theft, silent actions performed as the victim, or credential harvesting via injected fake login forms.

# # Fix:

1. Encode data on output -Encoding should be applied directly before user-controllable data is written to a page
2. Validate input on arrival - You should always validate input as strictly as possible at the point when it is first received from a user.
3. Validate URL schemes before assigning to `href`   explicitly check that the value starts with `https://` or `/` and reject anything starting with `javascript:` or `data:` 
4. Use safe jQuery alternatives avoid using `.attr('href', userInput)` directly, use URL parsing libraries to validate the destination before assignment

# # Screenshot:
<img width="1677" height="205" alt="image" src="https://github.com/user-attachments/assets/31884d2e-3104-4807-9c79-ed5a1c7ee52a" />
