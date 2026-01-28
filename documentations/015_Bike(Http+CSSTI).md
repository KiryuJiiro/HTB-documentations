# Bike (HTB) – Walkthrough Overview  
*Yeah yeah, Easy box, but you still wanted it documented like a thesis. Relax.*

## Overview
- **Box:** Bike (no, not the one you fell off as a kid)
- **OS:** Linux (obviously)
- **Difficulty:** Easy (and yet, here we are)
- **Focus:**  
  Web Enumeration → Server-Side Template Injection (SSTI) →  
  Template Engine Fingerprinting → Payload Crafting → Remote Code Execution  

The **Bike** machine demonstrates how insecure server-side template rendering can lead to full remote code execution—because trusting user input is dumb and developers keep doing it anyway.  
By identifying an SSTI vulnerability in a Node.js web application and fingerprinting the underlying template engine, an attacker can escape the template sandbox and execute system commands.  
This box highlights why blindly rendering user input is a speedrun to getting owned.

---

## Background Study

### Server-Side Template Injection (SSTI)
Server-Side Template Injection occurs when user-controlled input is embedded directly into a server-side template and rendered without proper sanitization.  
If exploitable, attackers can inject template expressions that execute on the server, leading to data disclosure or full command execution—aka *game over*.

### Template Engines (Handlebars)
Handlebars is a popular JavaScript template engine commonly used with Node.js and Express.  
It claims to be “logic-less,” which is adorable, because improper usage or unsafe helpers can still let attackers crawl out of the sandbox like demons.

### HTML Encoding and Output Escaping
HTML encoding converts special characters (`<`, `>`, `"`) into safe representations to prevent client-side attacks.  
Spoiler: this does **not** automatically protect server-side rendering. Attackers can encode payloads to slide right past this half-baked defense.

### Burp Suite
Burp Suite is used to intercept, modify, encode, and replay HTTP requests.  
In SSTI exploitation, it’s basically your best friend—unlike whoever told you encoding equals security.

---

## Attack Approach
- **Starting access level:** Unauthenticated external attacker (no creds, no mercy)
- **Expected attack surfaces:**
  - HTTP service (port 80, shocker)

Initial reconnaissance focused on identifying the web application stack and testing for template-based vulnerabilities.  
Enumeration revealed a Node.js Express application using a server-side template engine that was vulnerable to SSTI—because of course it was.

---

## Key Findings
- Node.js + Express web application
- Server-side template rendering of user-supplied input (🚩)
- Arithmetic template payloads triggered server-side errors
- Error messages leaked the use of the **Handlebars** template engine
- Handlebars sandbox escape was possible
- Remote command execution achieved via crafted payload

Basically: one bad design choice snowballed into total compromise. Classic.

---

## Exploitation

### Vulnerability Type
**Server-Side Template Injection (SSTI)**  
→ Template Sandbox Escape  
→ Remote Code Execution  

### Root Cause
User-controlled input was rendered directly inside a server-side Handlebars template without proper sanitization or strict sandboxing.  
Verbose error handling leaked internal implementation details, making template engine fingerprinting trivial.

### Attack Execution
Initial testing involved injecting arithmetic expressions such as `7*7` into the input field to test for SSTI.  
Instead of returning a calculated value, the application returned an error page—congratulations, it snitched on itself.

The error disclosed internal information indicating the use of the Handlebars template engine.  
Publicly known Handlebars SSTI payloads were then researched and adapted.

Direct use of `require` failed because it was not exposed globally in the template context (nice try).  
Further enumeration revealed access to the global `process` object.  
Using `process.mainModule.require`, the attacker indirectly accessed `require`, loaded the `child_process` module, and executed system commands via `.exec`.

Burp Suite was used to intercept and modify HTTP requests.  
URL encoding was required to bypass HTML encoding and ensure the payload was correctly interpreted during template rendering—because defenses were paper-thin.

---

## Access Gained
Remote Code Execution (RCE) was achieved on the target system through exploitation of a Server-Side Template Injection vulnerability in a Node.js Handlebars application.

---

## Mitigation / Defense
This attack could have been prevented by doing literally *any* of the following:
- Strict sanitization of user input before rendering
- Disabling or restricting dangerous template helpers
- Avoiding direct rendering of untrusted input
- Proper sandboxing of template engines
- Suppressing verbose error messages in production
- Using allowlists for template expressions

---

## Lessons Learned
- SSTI can lead directly to RCE (who knew? everyone)
- Error messages leak critical implementation details
- Not all template engines evaluate arithmetic expressions
- Sandbox escapes often rely on indirect access to globals
- Burp Suite is essential for payload manipulation
- HTML encoding ≠ server-side security (say it louder)
