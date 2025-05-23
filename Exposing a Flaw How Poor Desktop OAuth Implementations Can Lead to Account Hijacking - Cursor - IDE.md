

## Introduction

With the rise of desktop applications integrating OAuth for seamless sign-in, a growing number of apps leverage browser-based login flows to authenticate users. While this architecture provides usability benefits, it can also open doors to subtle but critical security vulnerabilities if not implemented correctly.

In this blog, we'll explore a hypothetical attack scenario in the context of a desktop app (like Cursor), walk through the OAuth flow, and highlight key concerns raised during investigation. We'll also propose how a strong OAuth implementation can mitigate these risks.

---

## How OAuth Login Works in Desktop Applications

### The Typical Flow:

1. **App Initiates Login**: The desktop app contacts the authentication server (`authenticator.cursor.sh`), which returns a `uuid`, `challenge`, and an internal `authorization_session_id`.
2. **Browser Login**: The app opens a browser to a URL like:

   ```
   https://www.cursor.com/loginDeepControl?uuid=abc123&challenge=xyz456
   ```
3. **User Authenticates**: The user logs in via Google or GitHub on the browser page.
4. **Auth Server Finalizes Login**: It binds the OAuth result to the `uuid`.
5. **App Polls for Result**: The desktop app uses the `uuid` to retrieve the login result.

---

## The Attack: Social Engineering Vulnerability

### Hypothetical Exploit

An attacker:

* Generates a login URL from their own app instance.
* Sends it to a target (e.g., CEO) under the guise of an article or resource.
* The target logs in with Google.
* The OAuth result links to the attacker’s `uuid`, logging them into the attacker’s app instance.

This type of attack is known as **Login CSRF** or **Login Injection** — using a legitimate OAuth flow to impersonate a user in a malicious client.

---

## Why It Works

The vulnerability arises when login results are accepted **without verifying the context or intent** of the login session.

### Key Weaknesses:

* `uuid` is visible in the URL and usable without
