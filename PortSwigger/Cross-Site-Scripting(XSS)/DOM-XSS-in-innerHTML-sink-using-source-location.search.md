# # Description

Lab: XSS- **DOM XSS in `innerHTML` sink using source `location.search`**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Apprentice </br>

Date Completed:  1 August 2026</br>

# # What was vulnerable:

The application's search results page includes a script that reads the search term directly from `location.search` and writes it into the page using `innerHTML`, without any encoding or sanitization. This creates a DOM-based XSS sink, since the value is inserted as raw HTML rather than as text.

# #Prerequisites

1. Understanding of DOM XSS sources and sinks
2. Understanding that `innerHTML`  does not execute script tags
3. Knowledge of HTML event handlers as XSS vectors
4. Difference between DOM XSS and reflected/stored XSS

# # What I did:

1. Loaded the lab and used the search box to submit a test string, then viewed page source and browser DevTools to locate client side JavaScript handling the search term, specifically looking for dangerous sinks like `document.write()`, `innerHTML`, or `eval()` .
2. The script reads the search query parameter using   ****`location.search`  ****and gives it to `innerHTML`  without using any encoding or sanitization.
3. Modifying the query to search for `<img src=x onerror=alert(1)>` .  Since the sink was `innerHTML`we ruled out `<script>` tags as browsers don't execute scripts inserted via  `innerHTML` used `<img src=x onerror=alert(1)>` instead to trigger JavaScript through an event handler.
4. Searching for this runs the script, triggering the alert to complete the lab.

# # Why it worked:

`innerHTML`  writes raw HTML into the document as it's parsed it does not encode or escape its input. Because the search term came straight from `location.search` (fully attacker-controllable, since it's just the URL) and was never encoded before being handed to `innerHTML` . But unlike `document.write()`  the `innerHTML`  sink cannot execute <script> tags as the browser doesn't execute scripts inserted this way as a security measure. so we construct a tag that executes JavaScript  using event handlers like `<img src=x onerror=alert(1)>`  as source ‘x’ does not exist the image tag runs the event handler responsible for errors which executes the command `alert(1)` 

# # Impact:

An attacker could craft a malicious URL containing this payload and send it to a victim. Since the XSS fires purely from the URL with no server-side storage involved, this is a classic DOM-based reflected XSS victims just need to click the link. Once triggered, the attacker's script runs in the victim's browser session, in the origin of the vulnerable site. Depending on what the site handles, this could enable session token/cookie theft, silent actions performed as the victim, or credential harvesting via injected fake login forms.

# # Fix:

1. Encode data on output -Encoding should be applied directly before user-controllable data is written to a page
2. Validate input on arriva**l -** You should always validate input as strictly as possible at the point when it is first received from a user.
3.  Use safe JavaScript methods instead of dangerous sinks replace `innerHTML`   with `textContent` or `innerText` which insert content as plain text rather than HTML, preventing injection entirely
4. Avoid passing URL parameters directly to JavaScript sinks  validate and sanitize `location.search` values before using them in any DOM manipulation function

# # Screenshot:
<img width="1732" height="205" alt="image" src="https://github.com/user-attachments/assets/ecf978b9-3f05-474d-98a4-e3f4a6dc1e43" />
