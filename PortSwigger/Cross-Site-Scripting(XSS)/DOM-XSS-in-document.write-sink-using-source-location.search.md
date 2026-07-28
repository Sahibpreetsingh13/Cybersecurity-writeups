# # Description

Lab: XSS- **DOM XSS in `document.write` sink using source `location.search`**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Apprentice </br>

Date Completed:  28 July 2026</br>

# # What was vulnerable:

The application's search results page includes a script that reads the search term directly from `location.search` and writes it into the page using `document.write()`, without any encoding or sanitization. This creates a DOM-based XSS sink, since the value is inserted as raw HTML rather than as text.

# # What I did:

1. Loaded the lab and used the search box to submit a test string, then viewed page source and browser DevTools to locate client side JavaScript handling the search term, specifically looking for dangerous sinks like `document.write()`, `innerHTML`, or `eval()` .
2. Found the vulnerable sink — the script reads the `search` query parameter and passes it straight into `document.write()`, which builds an `<img>` tag using the unsanitized value.
3. So we break out of the img attribute by modifying the search as `"><svg onload=alert(1)>` 
4. Searching for this runs the script, triggering the alert to complete the lab.

# # Why it worked:

`document.write()` writes raw HTML into the document as it's parsed — it does not encode or escape its input. Because the search term came straight from `location.search` (fully attacker-controllable, since it's just the URL) and was never encoded before being handed to `document.write()`, closing the `img` tag's `src` attribute with a quote and a `>` let me inject an arbitrary new tag. Using `svg onload` gave a reliable auto-executing event handler without needing user interaction like a click.

# # Impact:

An attacker could craft a malicious URL containing this payload and send it to a victim. Since the XSS fires purely from the URL with no server-side storage involved, this is a classic DOM-based reflected XSS victims just need to click the link. Once triggered, the attacker's script runs in the victim's browser session, in the origin of the vulnerable site. Depending on what the site handles, this could enable session token/cookie theft, silent actions performed as the victim, or credential harvesting via injected fake login forms.

# # Fix:

1. Encode data on output -Encoding should be applied directly before user-controllable data is written to a page
2. Validate input on arriva**l -** You should always validate input as strictly as possible at the point when it is first received from a user.
3. Use safe JavaScript methods instead of dangerous sinks replace `document.write()` with `textContent` or `innerText` which insert content as plain text rather than HTML, preventing injection entirely
4. Avoid passing URL parameters directly to JavaScript sinks  validate and sanitize `location.search` values before using them in any DOM manipulation function

# # Screenshot:
<img width="1496" height="203" alt="image" src="https://github.com/user-attachments/assets/e805f912-bee6-4a9b-aa64-3fe53e4e3c88" />
