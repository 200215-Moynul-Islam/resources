# Chapter 1 — Navigation Entry Point (User Action → Browser Start)

## 1.1 Entry Trigger

User initiates navigation by typeing something into the adrees bar and pressing enter.

---

## 1.2 URL vs Search Decision

Input:
example

Result:
→ Treated as search query

Input:
example.com

Result:
→ Treated as URL

Decision rule:

- Valid URL pattern → navigation
- Invalid URL pattern → search engine query

---

## 1.3 URL Normalization

Input:
example.com

Browser transforms to:
https://example.com/

Normalization rules:

- Add default scheme (https)
- Convert to absolute URL format
- Ensure valid navigation target

---

## 1.4 Navigation Request Creation

Browser action:
→ Creates internal navigation request

Properties:

- No network call yet
- Stored in browser process
- Used to coordinate next steps

---

## 1.5 Same-page vs Full Navigation

### Same-page navigation

Example:
https://site.com/page#section

Behavior:

- No network request
- Only client-side scroll update

---

### Full navigation

Example:
https://site.com/page → https://site.com/other

Behavior:

- Triggers network preparation
- Moves to next phase (network layer)

---

## 1.6 Navigation Pipeline Overview

Flow:  
User Input  
→ URL Parsing  
→ Normalization  
→ Navigation Decision  
→ Network Preparation

---

## 1.7 URL Anatomy (Essential)

Example:  
https://example.com/path?x=1#top

- Scheme: https
- Host: example.com
- Path: /path
- Query: ?x=1
- Fragment: #top

Rule:

- Fragment is never sent to server

---

## 1.8 Link Navigation

```html
<a href="/profile">Profile</a>
```

Flow:

- User clicks link
- Browser captures click event
- Browser evaluates navigation rules
- Navigation starts if not blocked

Key point:

- Link click is treated same as URL entry after validation

---

## 1.9 Navigation Blocking

JavaScript can stop navigation:

```javascript
event.preventDefault();
```

Effect:

- Stops default browser navigation
- Browser does not proceed further

Use case:

- SPA routing control
- Custom navigation handling

---

## 1.10 SPA Behavior (Single Page Applications)

Behavior:

- URL changes without full page reload
- Browser does not fetch a new HTML document
- JavaScript manages routing internally

Key idea:

- Navigation is simulated, not full reload-based

---

## 1.11 Mental Model

User Action  
→ Browser interprets input  
→ URL is normalized  
→ Navigation type is determined  
→ Browser prepares next phase (network layer)
