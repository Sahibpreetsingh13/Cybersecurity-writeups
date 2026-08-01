# # Description

Lab: XSS-  **Reflected XSS into a JavaScript string with angle brackets HTML encoded**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Apprentice </br>

Date Completed:  1 August 2026</br>

# # What was vulnerable:

The application has a search function whose input is reflected inside an existing `<script>` block, assigned to a JavaScript variable. The application HTML-encodes angle brackets (`<` and `>`) on reflection, but does not encode or escape single quotes. Since the reflection point is already inside a script context, breaking out of the JS string doesn't require any angle brackets at all, making the encoding ineffective against this sink.

# # What I did:

1. Loaded the lab and used the search function with a test input, then viewed the page source to see where the input was reflected.
2. Found the input was reflected inside a `<script>` block, assigned to a variable like `var searchTerms = 'input here';`.
3. Tested with a raw `<` character and confirmed it came back HTML-encoded as `&lt;`, while a single quote `'` was reflected unencoded.
4. Since angle brackets weren't needed to escape the string, crafted a payload that closes the existing string literal, injects a new JS expression, then re-opens a string to keep the remaining original code syntactically valid:`'-alert(1)-'`
5. Submitted the payload into the search box, which triggered `alert(1)` and solved the lab.

# # Why it worked:

The application's encoding was scoped to protect against HTML injection (new tags), so it filtered angle brackets on reflection. But the injection point was already inside a `<script>` block, so no new tags were ever needed — only the quote terminating the string literal mattered, and that character was never encoded. Prefixing the payload with `'` closed the original string early, letting `alert(document.domain)` execute as a real JS expression instead of being treated as harmless string content, with the trailing `'` absorbing the rest of the original line so nothing broke.

# # Impact:

An attacker could craft a URL containing this payload in the search parameter and send it to a victim. Since the script executes purely from reflected input with no server-side storage involved, this is a classic reflected XSS - the victim just needs to load the crafted link. The injected JavaScript would run in the origin of the vulnerable site, in the victim's session. Depending on what the site handles, this could enable session token/cookie theft, silent actions performed as the victim, or credential harvesting via injected fake login forms.

# # Fix:

1. Encode data on output using context-aware encoding - a value reflected inside a JavaScript string literal needs JS-string escaping (escaping `'`, `"`, `\`, and encoding `<` as `\x3C`), not just HTML encoding.
2.  Avoid writing user input directly into inline `<script>` blocks - pass data through `data-*` attributes or a non-executable JSON block (e.g. `<script type="application/json">`) and read it with `JSON.parse()` instead.

# # Screenshot:
<img width="1316" height="206" alt="image" src="https://github.com/user-attachments/assets/1df1e36c-dcac-4d99-bf86-2b9607bd0780" />
