# # Description

Lab:  Authentication - 2FA simple bypass</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Apprentice</br>

Date Completed: 05 October 2026</br>

# # What was vulnerable:

The application issued a fully authenticated session after the first login factor succeeded, before the 2FA code was ever verified.

# # What I did:

1. Opened the application and logged in using the given credentials.
2. The application asked for a verification code to complete the 2FA, after completing the verification step we land on the `/my-account` page.
3. Logged out, then logged in with the victim's provided username and password. The application redirected to a `/login2` page asking for the emailed verification code.
4. Instead of entering a code, manually navigated to `/my-account` by editing the URL directly. The page loaded successfully, confirming the session cookie issued after step 1 was already fully authenticated and the 2FA step was never actually enforced server-side.

# # Why it worked:

The application set an authenticated session cookie immediately after the first factor succeeded, rather than only after both factors were verified. The `/login2` verification page was purely a client-side/UI gate, it had no server-side check preventing already authenticated users from navigating directly to protected pages. This meant the 2FA step added no real security.

# # Impact:

An attacker with valid credentials can access all functionality available to that account for administrator accounts this means full application control, user management, and potentially access to internal infrastructure and sensitive data. Because 2FA added no real barrier here, any credential compromise phishing, breach reuse, credential stuffing fully bypasses the account's intended second layer of defense, not just the first.

# # Fix:

1. Do not issue a fully privileged session until all required authentication factors have been completed  use an intermediate, limited-privilege session state during the 2FA step that only grants access to the verification endpoint.
2. Enforce 2FA completion server-side on every protected route, not just by controlling which page the user is redirected to client-side.
3. Invalidate or downgrade the session if a user attempts to access protected resources before completing 2FA, and log the attempt as suspicious.

# # Screenshot:
<img width="1445" height="201" alt="image" src="https://github.com/user-attachments/assets/c02525fb-0640-4646-a84d-9d7851a1e58b" />
