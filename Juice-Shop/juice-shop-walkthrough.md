# OWASP Juice Shop - Complete Beginner's Walkthrough

**Compatible with OWASP Juice Shop v19.0.0**

A comprehensive guide for cybersecurity beginners to learn web application security through hands-on practice.

---

## Table of Contents
1. [Getting Started](#getting-started)
2. [Understanding the Challenge System](#understanding-the-challenge-system)
3. [Easy Challenges (1-Star)](#easy-challenges-1-star)
4. [Medium Challenges (2-Star)](#medium-challenges-2-star)
5. [Tools and Techniques](#tools-and-techniques)
6. [Learning Resources](#learning-resources)

---

## Getting Started

### Access Juice Shop
- **URL**: http://localhost:3000
- Open Firefox in your Kali VM
- The application simulates a real e-commerce juice shop

### First Task: Find the Score Board
The Score Board tracks all challenges and your progress. Finding it is actually the first challenge!

---

## Understanding the Challenge System

### Challenge Ratings
- 1-Star: Beginner-friendly, focuses on reconnaissance
- 2-Star: Requires basic exploitation techniques
- 3-Star: Intermediate level attacks
- 4-Star: Advanced exploitation
- 5-Star: Expert level challenges
- 6-Star: Extremely difficult

### Success Notifications
- Instant feedback when you solve a challenge
- Notifications don't auto-dismiss (you must close them)
- Tip: Hold Shift and click X to close multiple notifications at once

### Progress Saving
- Your progress auto-saves to browser cookies
- Persists for 30 days
- Survives server restarts

---

## Easy Challenges (1-Star)

### 1. Score Board
**Target Endpoint**: http://localhost:3000/#/score-board

**Vulnerability Type**: Forced Browsing / Information Disclosure

**What You'll Learn**: Applications often have hidden administrative or tracking pages that aren't linked but are still accessible through direct URL access.

**Solution Method 1 - Source Code Analysis**:
1. Open Firefox Developer Tools (F12)
2. Go to Debugger tab (or Sources in Chrome)
3. Expand the file list and open main-es2018.js or main.js
4. Press Ctrl+F and search for "score"
5. Look for routing paths like {path: "score-board"...}
6. Navigate to: http://localhost:3000/#/score-board

**Solution Method 2 - HTML Comments**:
1. Right-click on the page and select "View Page Source"
2. Search for "score" in the HTML
3. You may find commented-out navigation

**Solution Method 3 - Educated Guessing**:
- Notice that Juice Shop uses kebab-case (words separated by hyphens) for routes
- Try common admin page names: score-board, scoreboard, dashboard

**Tools Used**: Browser Developer Tools

**Security Lesson**: 
- Never rely on "security through obscurity"
- Hidden URLs are not secure
- Remove commented-out code before production deployment
- Implement proper access controls instead of hiding pages

---

### 2. DOM XSS
**Target Endpoint**: Search bar (any page)

**Vulnerable Parameter**: q (search query)

**Vulnerability Type**: DOM-based Cross-Site Scripting (XSS)

**What You'll Learn**: XSS allows attackers to inject malicious JavaScript that executes in victims' browsers. DOM XSS occurs when client-side JavaScript processes user input unsafely.

**Solution**:
1. Locate the search bar at the top of the page
2. Enter this payload: <iframe src="javascript:alert('XSS')">
3. Press Enter or click Search
4. An alert dialog will pop up, proving XSS is possible

**Alternative Payloads**:
```
<img src=x onerror="alert('XSS')">
<svg onload="alert('XSS')">
<script>alert('XSS')</script>
```

**Why It Works**:
- The search term is reflected in the DOM without sanitization
- The browser interprets the HTML/JavaScript and executes it
- No server-side filtering is applied to search queries

**Tools Used**: Browser only

**Security Lesson**:
- Always sanitize user input before inserting into the DOM
- Use Content Security Policy (CSP) headers
- Encode output appropriately (HTML encoding for HTML context)
- Never trust client-side input
- Use frameworks that auto-escape by default (like React, Angular with proper configuration)

---

### 3. Confidential Document
**Target Endpoint**: http://localhost:3000/ftp/acquisitions.md

**Vulnerability Type**: Improper Access Control / Directory Listing

**What You'll Learn**: Sensitive files should never be stored in publicly accessible directories, and directory listings should be disabled.

**Solution**:
1. Navigate to the "About Us" page from the side menu
2. Look for links to legal documents
3. Click on "Check out our boring terms of use"
4. Notice the URL pattern: http://localhost:3000/ftp/legal.md
5. Manually browse to: http://localhost:3000/ftp/
6. You'll see a directory listing with multiple files
7. Download acquisitions.md - this is the confidential document

**Files You'll Find**:
- legal.md - Terms of use
- acquisitions.md - CONFIDENTIAL acquisition plans
- Various other files depending on your Juice Shop version

**Why It Works**:
- The /ftp/ directory has directory listing enabled
- No authentication required to access these files
- Files are stored in a web-accessible location

**Tools Used**: Browser only

**Security Lesson**:
- Disable directory listings on web servers
- Store sensitive files outside the web root
- Implement authentication for sensitive documents
- Use proper file permissions
- Consider encrypting sensitive files at rest
- Implement download logging and monitoring

---

### 4. Error Handling
**Target Endpoint**: Various API endpoints (try invalid requests)

**Vulnerability Type**: Improper Error Handling / Information Disclosure

**What You'll Learn**: Detailed error messages can reveal sensitive system information like database structure, file paths, and software versions to attackers.

**Solution Method 1 - API Manipulation**:
1. Open Developer Tools (F12) → Network tab
2. Browse the shop and add items to basket
3. Look for API calls like /rest/basket/1
4. Try accessing: http://localhost:3000/rest/basket/invalid
5. Or: http://localhost:3000/api/Feedbacks/invalid
6. Check the response for detailed error messages with stack traces

**Solution Method 2 - SQL Injection Error**:
1. Try various API endpoints with SQL injection characters
2. Example: http://localhost:3000/rest/products/search?q='
3. Look for database error messages revealing table names, column names

**What to Look For**:
- Stack traces showing file paths
- Database error messages
- Framework version information
- Internal IP addresses
- Technology stack details

**Tools Used**: Browser Developer Tools, Burp Suite (optional)

**Security Lesson**:
- Use generic error messages in production
- Log detailed errors server-side only
- Never expose stack traces to users
- Implement custom error pages (404, 500, etc.)
- Sanitize all error messages before displaying
- Use error monitoring tools (Sentry, Rollbar) for internal tracking

---

### 5. Missing Encoding
**Target Endpoint**: http://localhost:3000/#/photo-wall

**Vulnerable File**: Photo with emoji and special characters in filename

**Vulnerability Type**: Improper URL Encoding

**What You'll Learn**: Special characters and Unicode must be properly encoded in URLs to prevent access issues and potential security vulnerabilities.

**Solution**:
1. Navigate to Photo Wall: http://localhost:3000/#/photo-wall
2. Open Developer Tools (F12) → Inspector/Elements tab
3. Inspect one of the images (particularly Bjoern's cat photo)
4. Look at the image source with emoji and hash symbols in the filename
5. To access directly, URL-encode the special characters:
   - Emoji becomes percent-encoded
   - Hash # becomes %23
6. Access the properly encoded URL

**Why It Works**:
- The application doesn't properly encode special characters in filenames
- Direct URL access without encoding fails
- Properly encoded URLs work

**Tools Used**: Browser Inspector, URL Encoder

**Security Lesson**:
- Always URL-encode special characters
- Sanitize filenames during upload
- Avoid special characters in filenames
- Use UUIDs or hashes for uploaded files
- Implement consistent encoding across the application

---

### 6. Outdated Allowlist
**Target Endpoint**: http://localhost:3000/redirect?to=[URL]

**Vulnerability Type**: Open Redirect / Allowlist Bypass

**What You'll Learn**: URL redirects must be strictly validated to prevent attackers from redirecting users to malicious sites.

**Solution**:
1. Look for external links in the application
2. Check the footer, About page, or social media links
3. Notice redirect URLs with blockchain cryptocurrency addresses
4. The challenge requires accessing an outdated crypto address
5. Try the redirect endpoint with various cryptocurrency addresses
6. Or find deprecated addresses by searching source code for "blockchain" or "bitcoin"

**Why It Works**:
- The allowlist contains outdated cryptocurrency addresses
- The redirect mechanism still accepts them
- No proper validation of allowed destinations

**Tools Used**: Browser, Source Code Analysis

**Security Lesson**:
- Maintain updated allowlists
- Implement strict redirect validation
- Use tokens instead of direct URLs for redirects
- Log redirect attempts for monitoring
- Consider removing redirect parameters entirely
- Use relative redirects when possible

---

### 7. Privacy Policy
**Target Endpoint**: http://localhost:3000/#/privacy-security/privacy-policy

**Vulnerability Type**: Poor Usability / Accessibility Issue

**What You'll Learn**: Important legal documents must be easily accessible to users.

**Solution Method 1 - For Logged-In Users**:
1. Log into the application
2. Click your account menu (top right)
3. Look for "Privacy & Security" option
4. Click "Privacy Policy"

**Solution Method 2 - Direct URL Access**:
1. Browse to: http://localhost:3000/#/privacy-security/privacy-policy

**Solution Method 3 - Source Code Analysis**:
1. Open Developer Tools (F12) → Debugger
2. Search main.js for "privacy"
3. Find the routing path

**Why This Is a Challenge**:
- Privacy policy is not prominently linked for non-logged-in users
- Required by law in many jurisdictions (GDPR, CCPA)
- Poor user experience

**Tools Used**: Browser, Developer Tools

**Security Lesson**:
- Make privacy policies easily accessible
- Link from footer and registration pages
- Comply with privacy regulations (GDPR, CCPA, etc.)
- Keep policies up-to-date
- Use clear, understandable language
- Version control policy changes

---

### 8. Repetitive Registration
**Target Endpoint**: http://localhost:3000/api/Users/ (Registration API)

**Vulnerability Type**: Business Logic Flaw / Input Validation Bypass

**What You'll Learn**: The "DRY" (Don't Repeat Yourself) principle - applications should validate that required fields aren't duplicated unnecessarily.

**Solution**:
1. Go to registration page: http://localhost:3000/#/register
2. Fill out the form with email and password
3. Key: Leave the "Repeat Password" field empty
4. Open Developer Tools (F12) → Network tab
5. Submit the registration
6. Intercept or modify the request to remove the password repeat field
7. Or use Burp Suite to capture and modify the POST request

**API Request (Normal)**:
```
{
  "email": "test@test.com",
  "password": "Password123",
  "passwordRepeat": "Password123",
  "securityQuestion": {...},
  "securityAnswer": "answer"
}
```

**Modified Request**:
```
{
  "email": "test@test.com",
  "password": "Password123",
  "securityQuestion": {...},
  "securityAnswer": "answer"
}
```

**Tools Used**: Browser Developer Tools, Burp Suite

**Security Lesson**:
- Validate all inputs server-side
- Don't rely on client-side validation
- Implement consistent validation rules
- Check for both presence and correctness
- Follow secure coding principles

---

### 9. Zero Stars
**Target Endpoint**: http://localhost:3000/api/Feedbacks/ (Customer Feedback API)

**Vulnerability Type**: Improper Input Validation / Client-Side Bypass

**What You'll Learn**: Client-side validation can easily be bypassed. All validation must be enforced server-side.

**Solution Method 1 - Browser DevTools**:
1. Navigate to Customer Feedback page
2. Fill out the feedback form (notice the rating slider only allows 1-5 stars)
3. Open Developer Tools (F12) → Network tab
4. Submit feedback with 1 star
5. Find the POST request to /api/Feedbacks/
6. Right-click → "Edit and Resend"
7. Modify the JSON payload, change "rating": 1 to "rating": 0
8. Send the modified request

**Solution Method 2 - Burp Suite**:
1. Configure browser to use Burp proxy
2. Submit feedback with any rating
3. In Burp: Proxy → HTTP history
4. Find the /api/Feedbacks/ POST request
5. Right-click → Send to Repeater
6. In Repeater, modify "rating": 1 to "rating": 0
7. Click "Send"

**Original Request**:
```
POST /api/Feedbacks/ HTTP/1.1
Content-Type: application/json

{
  "captchaId": 1,
  "captcha": "5",
  "comment": "Bad service",
  "rating": 0
}
```

**Tools Used**: Browser Developer Tools, Burp Suite

**Security Lesson**:
- Never trust client-side validation
- Implement server-side input validation
- Validate data types, ranges, and formats
- Use allowlists, not denylists
- Return meaningful error messages for invalid input
- Log suspicious input attempts

---

### 10. Bully Chatbot
**Target Endpoint**: Customer Support Chat (requires login)

**Vulnerability Type**: Business Logic Flaw

**What You'll Learn**: Chatbots should have proper response handling and not give away sensitive information through manipulation.

**Solution**:
1. Log into the application (or create an account)
2. Access the Support Chat from the side menu
3. Start chatting with the bot
4. Try various aggressive or demanding messages
5. Ask repeatedly for coupons, discounts, or deals
6. Use keywords like: "coupon", "discount code", "promo"
7. Be persistent and demanding
8. The bot will eventually give you a coupon code to make you go away

**Example Conversation**:
- You: "I want a discount!"
- Bot: "I can help you with that"
- You: "Give me a coupon now!"
- Bot: "Here's a coupon code: [CODE]"

**Tools Used**: Browser only

**Security Lesson**:
- Implement rate limiting on chatbots
- Design chatbot responses carefully
- Don't let bots distribute sensitive information
- Add human verification for valuable actions
- Monitor chatbot conversations
- Implement escalation to human agents

---

### 11. Exposed Metrics
**Target Endpoint**: http://localhost:3000/metrics

**Vulnerability Type**: Information Disclosure

**What You'll Learn**: Application metrics and monitoring endpoints should be protected with authentication to prevent information leakage.

**Solution**:
1. The challenge mentions "Prometheus" (a popular monitoring system)
2. Prometheus typically uses /metrics endpoint
3. Navigate to: http://localhost:3000/metrics
4. You'll see detailed application metrics in Prometheus format

**Information Exposed**:
- Request counts and timing
- System resource usage
- Application performance data
- Potentially sensitive operational information
- Technology stack details

**Tools Used**: Browser only

**Security Lesson**:
- Protect monitoring endpoints with authentication
- Use separate internal networks for monitoring
- Limit metrics to necessary information only
- Don't expose internal infrastructure details
- Implement IP whitelisting for monitoring endpoints
- Use VPNs or private networks for admin tools

---

### 12. Security Policy
**Target Endpoint**: http://localhost:3000/.well-known/security.txt

**Vulnerability Type**: Missing Security Policy (Good Practice)

**What You'll Learn**: Organizations should publish security policies telling security researchers how to report vulnerabilities responsibly.

**Solution**:
1. Navigate to: http://localhost:3000/.well-known/security.txt
2. This follows RFC 9116 standard for security.txt files
3. The file contains contact information for security reports

**Note**: The path is /.well-known/ not /#/.well-known/

**What security.txt Contains**:
- Contact information for security team
- Preferred languages
- Disclosure policy
- PGP keys for encrypted communication

**Tools Used**: Browser only

**Security Lesson**:
- Publish a security.txt file
- Follow RFC 9116 standard
- Provide clear reporting channels
- Implement responsible disclosure program
- Respond promptly to reports
- Consider bug bounty programs

---

### 13. Web3 Sandbox
**Target Endpoint**: http://localhost:3000/#/web3-sandbox

**Vulnerability Type**: Accidental Deployment / Information Disclosure

**What You'll Learn**: Development tools and sandboxes should never be deployed to production.

**Solution**:
1. Similar to finding the Score Board
2. Use Developer Tools → Debugger
3. Search for "web3" or "sandbox" in JavaScript files
4. Find the route
5. Navigate to: http://localhost:3000/#/web3-sandbox
6. You'll find a smart contract development environment

**Why This Is Dangerous**:
- Development tools in production
- Could be used for testing vulnerabilities
- May expose backend systems
- Indicates poor deployment practices

**Tools Used**: Browser Developer Tools

**Security Lesson**:
- Never deploy development tools to production
- Use separate environments (dev, staging, production)
- Implement proper deployment pipelines
- Review code before deployment
- Use feature flags to disable dev features
- Audit production deployments regularly

---

### 14. Mass Dispel
**Target Endpoint**: Challenge notification system

**Vulnerability Type**: UX Feature (not a vulnerability)

**What You'll Learn**: Reading documentation and understanding application features.

**Solution**:
1. Solve multiple challenges to get several notification popups
2. Hold Shift key
3. Click the X button on any notification
4. All notifications will close at once

**Note**: If you don't have multiple notifications:
- Stop and restart the Docker container
- Notifications regenerate on server start
- Or solve multiple challenges in quick succession

**Tools Used**: Keyboard + Mouse

**Security Lesson**:
- Read documentation thoroughly
- Understanding features can help in security testing
- UX features might reveal application behavior

---

## Medium Challenges (2-Star)

### 15. Admin Section
**Target Endpoint**: http://localhost:3000/#/administration

**Vulnerability Type**: Forced Browsing + Authentication Required

**What You'll Learn**: Finding admin pages and bypassing authentication to access them.

**Solution**:
1. Find the Admin Section:
   - Use Developer Tools → Debugger
   - Search for "admin" in main.js
   - Find route
   - URL: http://localhost:3000/#/administration

2. Access Requires Admin Login (see Login Admin challenge below)

**Alternative Discovery**:
- Try common admin paths: /admin, /#/administration, /administration
- Check source code comments
- Analyze application routes

**Tools Used**: Browser Developer Tools

**Security Lesson**:
- Implement proper authentication and authorization
- Use role-based access control (RBAC)
- Don't rely on hidden URLs
- Log access attempts to admin pages
- Implement multi-factor authentication for admin accounts

---

### 16. Login Admin
**Target Endpoint**: http://localhost:3000/rest/user/login

**Vulnerable Parameter**: Email field (SQL injection)

**Vulnerability Type**: SQL Injection

**What You'll Learn**: SQL injection allows attackers to manipulate database queries by injecting malicious SQL code.

**Solution**:
1. Go to the login page: http://localhost:3000/#/login
2. Find the admin email (check product reviews or feedback)
   - Admin email: admin@juice-sh.op
3. SQL Injection in Email field: admin@juice-sh.op'--
4. Password: anything (it won't be checked)
5. Click Login

**Why It Works**:
```
-- Original Query:
SELECT * FROM Users WHERE email='admin@juice-sh.op'--' AND password='anything'

-- After '--' comments out the rest:
SELECT * FROM Users WHERE email='admin@juice-sh.op'
```

The '--' closes the string and comments out the password check.

**Alternative Payloads**:
```
' OR 1=1--
admin@juice-sh.op' OR '1'='1
' OR 'x'='x'--
```

**Tools Used**: Browser only

**Security Lesson**:
- Use parameterized queries / prepared statements
- Never concatenate user input into SQL queries
- Implement ORM frameworks (Sequelize, Hibernate)
- Apply input validation
- Use least privilege for database accounts
- Implement account lockout mechanisms
- Log failed login attempts

---

### 17. Login MC SafeSearch
**Target Endpoint**: http://localhost:3000/rest/user/login

**Vulnerability Type**: SQL Injection (targeting specific user)

**What You'll Learn**: SQL injection can target any specific user account.

**Solution**:
1. Find MC SafeSearch's email in the application
   - Check customer feedback
   - Email: mc.safesearch@juice-sh.op
2. Use SQL injection: mc.safesearch@juice-sh.op'--
3. Password: anything
4. Login successful

**Why It Works**: Same SQL injection technique as admin login, but targeting a different user.

**Tools Used**: Browser only

**Security Lesson**:
- Same as Login Admin - SQL injection affects all users
- No user account is safe from SQL injection
- Defense must be implemented at the query level

---

### 18. View Basket
**Target Endpoint**: http://localhost:3000/rest/basket/[ID]

**Vulnerable Parameter**: Basket ID (IDOR - Insecure Direct Object Reference)

**Vulnerability Type**: Broken Access Control

**What You'll Learn**: Applications must verify users have permission to access requested resources, not just authenticate them.

**Solution**:
1. Log into the application
2. Add items to your basket
3. Open Developer Tools (F12) → Network tab
4. Click on the basket icon
5. Observe API request: GET /rest/basket/1
6. The number (1) is your basket ID
7. Change the URL to: http://localhost:3000/rest/basket/2
8. You can now see another user's basket!

**Try Different IDs**:
```
/rest/basket/1
/rest/basket/2
/rest/basket/3
```

**API Response Example**:
```
{
  "status": "success",
  "data": {
    "id": 2,
    "Products": [...]
  }
}
```

**Tools Used**: Browser Developer Tools

**Security Lesson**:
- Always verify user owns the requested resource
- Implement proper authorization checks
- Use session-based resource access
- Don't expose sequential IDs
- Use UUIDs instead of integers for resources
- Log unauthorized access attempts
- Return same error for unauthorized and not found

---

### 19. Five-Star Feedback
**Target Endpoint**: http://localhost:3000/api/Feedbacks/[ID] (DELETE method)

**Vulnerability Type**: Broken Access Control + Rate Limiting Bypass

**What You'll Learn**: Users shouldn't be able to delete other users' content, and rate limiting should prevent automated actions.

**Solution Method 1 - Browser DevTools**:
1. Log in as admin (or any user)
2. Go to Administration panel: /#/administration
3. View the feedback section
4. Open Developer Tools → Network tab
5. Delete a 5-star feedback entry
6. Observe the DELETE request: DELETE /api/Feedbacks/[ID]
7. Note the feedback IDs
8. Repeat for all 5-star feedback entries

**Solution Method 2 - Burp Suite Automation**:
1. Capture a DELETE request
2. Send to Intruder
3. Set the ID as payload position
4. Use sequential numbers as payloads
5. Filter responses for 5-star ratings
6. Delete them all

**Tools Used**: Browser Developer Tools, Burp Suite

**Security Lesson**:
- Implement proper authorization for delete operations
- Users should only delete their own content
- Implement rate limiting
- Log deletion events
- Consider soft deletes instead of hard deletes
- Require additional confirmation for bulk operations

---

### 20. Password Strength
**Target Endpoint**: http://localhost:3000/rest/user/login

**Vulnerability Type**: Weak Password Policy + Password Guessing

**What You'll Learn**: Admin accounts often use weak, guessable passwords despite best practices.

**Solution**:
1. Try logging in as admin with common passwords
2. Email: admin@juice-sh.op
3. Try passwords:
   - admin123
   - password
   - admin
   - 123456
   - admin123 (This works!)

**Tools Used**: Browser, Burp Suite (optional)

**Security Lesson**:
- Enforce strong password policies
  - Minimum 12+ characters
  - Mix of uppercase, lowercase, numbers, symbols
  - No common passwords
  - No dictionary words
- Implement account lockout after failed attempts
- Use multi-factor authentication
- Monitor for brute force attacks
- Use CAPTCHA after multiple failed attempts
- Implement password complexity requirements

---

### 21. CAPTCHA Bypass
**Target Endpoint**: http://localhost:3000/api/Feedbacks/

**Vulnerability Type**: Broken Anti-Automation / CAPTCHA Bypass

**What You'll Learn**: CAPTCHA must be properly validated server-side to prevent automated submissions.

**Solution**:
1. Go to customer feedback page
2. Open Developer Tools → Network tab
3. Fill out and submit one feedback
4. Capture the POST request to /api/Feedbacks/
5. Note the CAPTCHA structure in the request
6. Use "Edit and Resend" or Burp Repeater
7. Send 10+ requests rapidly (within 10 seconds)
8. Key: Reuse the same CAPTCHA answer multiple times

**Request Example**:
```
{
  "captchaId": 10,
  "captcha": "7",
  "comment": "Test feedback",
  "rating": 5
}
```

**Using Burp Suite**:
1. Send request to Repeater
2. Click Send 10+ times rapidly
3. OR use Intruder with null payloads
4. Set thread count high

**Tools Used**: Browser Developer Tools, Burp Suite

**Security Lesson**:
- CAPTCHA tokens should be single-use
- Invalidate CAPTCHA after first use
- Implement proper session tracking
- Use modern CAPTCHA solutions (reCAPTCHA v3)
- Rate limit by IP address
- Monitor for automated behavior patterns
- Implement progressive delays

---

### 22. Login Bender
**Target Endpoint**: http://localhost:3000/rest/user/login

**Vulnerability Type**: SQL Injection (specific user targeting)

**What You'll Learn**: Finding user emails and using SQL injection to access their accounts.

**Solution**:
1. Find Bender's email in the application
   - Check product reviews
   - Look in customer feedback
   - Email: bender@juice-sh.op
2. SQL Injection: bender@juice-sh.op'--
3. Password: anything
4. Login successful

**Tools Used**: Browser only

**Security Lesson**:
- Same SQL injection vulnerability affects all users
- Demonstrates horizontal privilege escalation
- Must fix at the query level, not per-user

---

### 23. User Credentials
**Target Endpoint**: http://localhost:3000/rest/products/search?q=[QUERY]

**Vulnerable Parameter**: Search query (SQL injection)

**Vulnerability Type**: SQL Injection (Union-based)

**What You'll Learn**: Advanced SQL injection can extract data from other database tables.

**Solution**:
1. Go to product search
2. Use UNION SQL injection to query Users table
3. Payload examples:

**Basic Union Injection**:
```
')) UNION SELECT id, email, password, '4', '5', '6', '7', '8' FROM Users--
```

**Step-by-Step**:
1. Search bar: http://localhost:3000/#/search?q=test
2. Open Network tab in DevTools
3. Try injecting: ')) UNION SELECT NULL--
4. Adjust NULL count until no error (match column count)
5. Replace NULLs with actual columns: id, email, password
6. The search results will display user credentials!

**What You'll See**:
- All user emails and password hashes
- Admin account details
- Customer information

**Tools Used**: Browser Developer Tools

**Security Lesson**:
- Use parameterized queries ALWAYS
- Implement principle of least privilege for DB accounts
- Never store passwords in plain text
- Use strong hashing algorithms (bcrypt, Argon2)
- Sanitize all user inputs
- Implement Web Application Firewall (WAF)
- Use ORM frameworks properly

---

### 24. Empty User Registration
**Target Endpoint**: http://localhost:3000/api/Users/

**Vulnerability Type**: Input Validation Bypass

**What You'll Learn**: Server-side validation can be incomplete, allowing invalid data.

**Solution**:
1. Go to registration page
2. Open Developer Tools → Network tab
3. Fill form minimally (email, password)
4. Submit and capture the POST request
5. Edit and Resend with modifications:

**Modified Request**:
```
{
  "email": "",
  "password": "",
  "passwordRepeat": "",
  "securityQuestion": {
    "id": 1,
    "question": "Your eldest siblings middle name?",
    "createdAt": "2024-01-01",
    "updatedAt": "2024-01-01"
  },
  "securityAnswer": "anything"
}
```

**Using Burp Suite**:
1. Intercept registration POST request
2. Modify email and password to empty strings
3. Forward the request

**Tools Used**: Browser Developer Tools, Burp Suite

**Security Lesson**:
- Validate all fields server-side
- Check for empty strings, null values, whitespace
- Don't trust client-side validation
- Implement comprehensive input validation
- Use validation libraries/frameworks
- Return meaningful error messages

---

### 25. Privacy Policy Inspection
**Target Endpoint**: http://localhost:3000/#/privacy-security/privacy-policy

**Vulnerability Type**: Challenge requires reading

**What You'll Learn**: Users rarely read privacy policies, but they contain important information.

**Solution**:
1. Navigate to: http://localhost:3000/#/privacy-security/privacy-policy
2. Actually read the entire privacy policy
3. Look for hidden elements or specific text
4. At the bottom, you'll find a clickable element or link
5. Click on the "hidden" link or element that proves you read it

**What to Look For**:
- Small text at the bottom
- A clickable phrase or word
- Something that stands out as unusual
- May require scrolling to the very bottom

**Tools Used**: Browser only, patience

**Security Lesson**:
- Users don't read policies (security awareness issue)
- Important information gets ignored
- Need better ways to communicate security information
- Consider progressive disclosure
- Use plain language
- Highlight critical information

---

### 26. Manipulate Basket
**Target Endpoint**: http://localhost:3000/api/BasketItems/

**Vulnerability Type**: Broken Access Control / IDOR

**What You'll Learn**: Users should only be able to modify their own shopping baskets.

**Solution**:
1. Create two user accounts or log in as one user
2. Add items to your basket
3. Note your basket ID (from View Basket challenge)
4. Open Developer Tools → Network tab
5. Add an item to your basket
6. Capture the POST request to /api/BasketItems/
7. Modify the BasketId parameter to another user's basket ID

**Request Example**:
```
POST /api/BasketItems/
{
  "ProductId": 1,
  "BasketId": 2,
  "quantity": 1
}
```

**Change BasketId from 1 (yours) to 2 (someone else's)**

**Tools Used**: Browser Developer Tools, Burp Suite

**Security Lesson**:
- Verify user owns the basket before adding items
- Implement proper session-based basket management
- Don't allow users to specify basket IDs
- Use server-side session to determine basket
- Log suspicious basket manipulation attempts

---

### 27. Deprecated Interface
**Target Endpoint**: http://localhost:3000/b2b/v2/orders

**Vulnerability Type
