---
id: 202606120827
title: Browser Storage - Local, Session, Cookies
aliases:
  - localstorage
  - sessionStorage
  - cookies
tags:
  - type/concept
  - lang/js
  - status/processing
  - topic/dom
date_created: 2026-06-12
mastery_level: 1
---

# Browser Storage: Local, Session, Cookies

> **TL;DR:** Browser storage lets websites remember you. **LocalStorage** is a permanent vault, **SessionStorage** is a temporary drawer (cleared when the tab closes), and **Cookies** are baggage tags sent back and forth to the server with every request.

## The Mental Model
Imagine you are visiting a library:
- **LocalStorage:** The library gives you a personal locker that you can access forever. Even if you go home and return next week, your stuff is still in there.
- **SessionStorage:** The library gives you a temporary desk drawer for your current visit. Once you pack up and leave (close the browser tab), the librarian empties the drawer.
- **Cookies:** Every time you ask a librarian for a book, they stick a small sticker on your shirt containing your name. Every time you walk up to them, they read the sticker to remember who you are.

---

## The Big Three Compared

| Feature | LocalStorage | SessionStorage | Cookies |
| :--- | :--- | :--- | :--- |
| **Capacity** | ~5MB - 10MB | ~5MB | ~4KB |
| **Expiry** | Never (must clear manually) | When tab/window closes | Custom expiration date |
| **Server Sent?**| No (client-side only) | No (client-side only) | Yes (sent with every request) |
| **Access** | HTML5 Web Storage API | HTML5 Web Storage API | `document.cookie` (clunky API) |

---

## 1. LocalStorage & SessionStorage
Both use the exact same key-value API under the hood. 

> [!IMPORTANT]
> **Strings Only:** Web Storage can **only** store strings. If you want to store an object or array, you must convert it to a JSON string first using `[[js-json-parse-and-stringify|JSON.stringify()]]`, and read it back with `[[js-json-parse-and-stringify|JSON.parse()]]`.

```javascript
// Saving data
localStorage.setItem("theme", "dark");

// Retrieving data
const currentTheme = localStorage.getItem("theme"); // "dark"

// Deleting data
localStorage.removeItem("theme");

// Clearing everything
localStorage.clear();
```

---

## 2. Cookies: The Server Connection
Cookies are older than Web Storage. Because they are automatically attached to network requests, they are primarily used for authentication (like tracking who is logged in).

- **Security Note:** JavaScript can read normal cookies via `document.cookie`. However, developers can set a cookie as `HttpOnly`. This prevents JavaScript from reading it, blocking cross-site scripting (XSS) token theft.

---

## Canonical Code Example

Here is how you save a user preferences object permanently, and a temporary token for the session:

```javascript
// --- LocalStorage Example (Permanent Preferences) ---
const userSettings = {
  theme: "dark",
  fontSize: "16px",
  notifications: true
};

// 1. We MUST stringify the object before saving
localStorage.setItem("user-preferences", JSON.stringify(userSettings));

// 2. To read it back, get the item and parse it back to an object
const savedData = localStorage.getItem("user-preferences");

if (savedData) {
  const parsedSettings = JSON.parse(savedData);
  console.log("Loaded Theme:", parsedSettings.theme); // "dark"
}


// --- SessionStorage Example (Temporary Session Auth Token) ---
// Save a token that vanishes when the user closes their tab
sessionStorage.setItem("authToken", "xyz123_temp_token");

console.log("Session Token:", sessionStorage.getItem("authToken"));


// --- Cookie Example (Small, Network-Bound String) ---
// Setting a simple cookie that expires in 1 day
document.cookie = "username=Hemanth; max-age=86400; path=/; SameSite=Lax";
console.log("Current Cookies:", document.cookie);
```

---

## Related
* [[MOC - JavaScript Core]] - Central index for JavaScript engine.
* [[js-primitive-vs-reference-types]] - Differentiating primitives from reference objects.
* [[js-dom-tree-and-element-selection]] - Selecting elements to retrieve inputs.
* [[js-json-parse-and-stringify]] - Serializing objects to storage strings.
