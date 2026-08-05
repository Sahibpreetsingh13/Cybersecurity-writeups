## # Description

Lab: XSS - **Reflected XSS into HTML context with all tags blocked except custom ones**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed: 5 August 2026</br>

## # What was vulnerable:

The lab's search function reflects user input into the HTML context. A WAF sits in front of the application and strips or blocks essentially every standard HTML tag (`<script>`, `<img>`, `<svg>`, `<body>`, `<iframe>`, etc.), but it does not account for custom (non-standard) tag names, which get reflected untouched.

## #Prerequisites:

1. Understanding of reflected XSS basics
2. Knowledge of WAF bypass techniques via tag fuzzing
3. Familiarity with Burp Intruder for fuzzing
4. Understanding of custom/non-standard HTML elements
5. Understanding of the `tabindex` attribute and URL fragment-triggered focus behavior

## # What I did:

1. Loaded the lab and confirmed the search parameter reflects input back into the page unencoded.
2. Tried standard payloads like `<script>alert(1)</script>` and `<img src=1 onerror=alert(1)>`  both were blocked by the WAF.
3. Ran the reflected parameter through Burp Intruder, fuzzing a wide list of both real and made-up tag names (`§tag§ onx=1`) to see which ones the WAF let through with a 200 status instead of blocking them outright.
4. Found that arbitrary, non-standard tag names (e.g. `xss`, or any string that isn't a real HTML tag) passed straight through — the WAF's blocklist only checked against known tag names, not the general shape of an opening tag.
5. Since a custom tag has no built-in events (no `onerror`, no `onload`) I needed a way to fire an event handler without any real user interaction. I attached `tabindex="1"` to the custom element, which makes it focusable, and gave it `id="x"`.
6. To trigger focus automatically, I relied on the browser behavior where navigating to a URL with a fragment identifier (`#x`) matching an element's `id` causes that element to receive focus on page load. Combined with `onfocus`, this fires the event with zero clicks.
7. Final injected payload: `<xss id=x onfocus=alert(document.cookie) tabindex=1>`
8. Hosted an exploit on the exploit server that redirects the victim to the vulnerable URL with the payload in the `search` parameter and `#x` appended, so the page loads already focused on the injected element: `<script> window.location = 'https://YOUR-LAB-ID.web-security-academy.net/?search=%3Cxss+id%3Dx+onfocus%3Dalert(document.cookie)+tabindex%3D1%3E#x';</script>`
9. Delivered the exploit to the victim, completing the lab.

## # Why it worked:

The WAF's blocklist was built around a fixed set of known HTML tag and event names rather than validating the general structure of injected markup. Because it never anticipated arbitrary custom tag names, anything that isn't a recognized element name slipped through untouched. A custom element has no native events of its own, so `tabindex` was needed to make it a focusable element, and the fragment identifier (`#x`) leveraged native browser behavior — jumping focus to the element whose `id` matches the fragment on page load — to trigger `onfocus` without any click, keystroke, or other victim interaction.

## # Impact:

Since the payload is reflected directly from the `search` parameter with no server-side storage, this is a classic reflected XSS: the victim only needs to open a single crafted link. The injected JavaScript executes in the origin of the vulnerable site under the victim's session, which could allow session token or cookie theft, silent actions performed on the victim's behalf, or injection of fake UI elements (e.g. credential-harvesting forms).

## # Fix:

1. **Encode data on output** - Any user-controllable data should be HTML-entity encoded immediately before being written into the page, regardless of what a WAF does upstream.
2. **Validate input on arrival** - Reject or strip characters like `<` and `>` from input as early as possible rather than relying on pattern-matching after the fact.
3. **Don't rely on tag/event blocklists** - A WAF that blocks known tag and event names is trivially bypassed by fuzzing for names it doesn't recognize, including custom elements. Output encoding should be the primary defense; the WAF should only be a secondary layer.
4. Consider disabling or restricting the browser's native fragment-based auto-focus behavior isn't practical to fix server-side, which is another reason output encoding — not input filtering — needs to be the real control.

## # Screenshot:
<img width="1245" height="202" alt="image" src="https://github.com/user-attachments/assets/27492742-a096-4c1c-a8b0-a6e3ebeb1424" />
