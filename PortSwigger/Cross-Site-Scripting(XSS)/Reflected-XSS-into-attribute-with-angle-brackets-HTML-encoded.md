# # Description

Lab: XSS-**Reflected XSS into attribute with angle brackets HTML-encoded**</br>

Platform: PortSwigger Web Academy</br>

Difficulty: Apprentice </br>

Date Completed:  29 July 2026</br>

# # What was vulnerable:

The search functionality reflected user input inside an HTML attribute value without sanitizing quotes angle brackets were HTML encoded but quote characters were not, allowing attribute injection without needing to break out of the tag entirely

# # What I did:

1. Identified the search functionality as a potential injection point. 
2. Submitted a alphanumeric string in the search box and intercepted the request using burp suite and sent the request to burp repeater 
3. Viewed the page source and found the search term reflected inside the value attribute of an input tag: `<input value="test1">`
4. Replaced the previous input with `"onmouseover="alert(1)`  and hovered over the search box to execute the command and complete the lab

# # Why it worked:

The original input was in a attribute called “value” in the input tag of HTML onmouseover is another attribute of the input tag and the starting double quote was used to close the value attribute. The onmouseover attribute is triggered when the user hovers the mouse over the tag. This is reflected XSS ,the payload travels in the request and is immediately reflected back in the response, executing in the victim's browser. Unlike stored XSS the payload isn't saved in the database the victim must be tricked into clicking a crafted link containing the payload. Angle brackets were HTML encoded by the application `<` became `&lt;` which prevented injecting new HTML tags. However quotes were not encoded, so closing the value attribute with `"` and injecting a new event handler attribute worked instead.

# # Impact:

An attacker who can execute arbitrary JavaScript in a victim's browser can perform any action the victim can stealing session cookies, making requests on their behalf, capturing keystrokes, redirecting to phishing pages, or defacing the page content.

# # Fix:

1. Encode data on output -Encoding should be applied directly before user-controllable data is written to a page
2. Validate input on arrival - You should always validate input as strictly as possible at the point when it is first received from a user.
3. Encode quotes in HTML attribute contexts HTML encoding angle brackets alone is insufficient. Attribute values must also have single and double quotes encoded as `&quot;` and `&#x27;` to prevent attribute injection

# # Screenshot:
<img width="1637" height="220" alt="image" src="https://github.com/user-attachments/assets/8031ba74-278d-4d7f-ad1c-b663bdc41296" />
