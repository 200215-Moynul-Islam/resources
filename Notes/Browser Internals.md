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

---

# Chapter 2 — From Address Bar → Connection (HTTP vs HTTPS)

## 2.1 Start Point

User enters:

http://example.com  
or  
https://example.com  

---

## 2.2 Step 1 — Browser parses the URL

The browser extracts:
- Scheme → HTTP (HyperText Transfer Protocol) or HTTPS (HyperText Transfer Protocol Secure)
- Host → example.com
- Port:
  - HTTP → 80
  - HTTPS → 443

Decision:
- If HTTP → no security required
- If HTTPS → TLS (Transport Layer Security) required

---

## 2.3 Step 2 — DNS Resolution (Domain Name System)

Goal:
Convert domain → IP address

---

### Step 2.3.1 — Browser checks cache

The browser checks:
→ “Do I already know this domain?”

If yes:
→ Uses cached IP

If no:
→ Asks operating system (OS)

---

### Step 2.3.2 — OS checks cache

The OS checks:
→ Local DNS cache

If found:
→ Returns IP to browser

If not:
→ Asks DNS resolver

---

### Step 2.3.3 — DNS resolver processes request

The DNS resolver (ISP or public DNS) receives:
→ “What is the IP of example.com?”

If cached:
→ Returns IP

If not:
→ Performs recursive lookup

---

### Step 2.3.4 — Resolver queries DNS hierarchy

The resolver contacts:

1. Root server → “Where is .com?”  
2. TLD (Top-Level Domain) server → “Where is example.com?”  
3. Authoritative DNS server → “Give me the IP”  

Authoritative server returns:
→ IP address

---

### Step 2.3.5 — Result propagation

Resolver → OS → Browser

All layers cache the result.

Final:
example.com → 93.x.x.x

---

## 2.4 Step 3 — TCP Connection (Transmission Control Protocol)

The browser connects to:
- IP + Port (80 or 443)

Handshake:
1. Browser → SYN  
2. Server → SYN-ACK  
3. Browser → ACK  

Result:
→ TCP connection established

---

## 2.5 Step 4 — Branch: HTTP vs HTTPS

---

### Case A — HTTP (No Security)

The browser:
→ Directly sends HTTP request

Example:

GET / HTTP/1.1  
Host: example.com  

---

#### What happens on network

Data is sent as plain text.

Middleman (WiFi owner, ISP, attacker):
- Can read headers
- Can read cookies
- Can read request body
- Can modify response

---

### Case B — HTTPS (Secure)

The browser:
→ Starts TLS (Transport Layer Security) before sending HTTP

---

## 2.6 Step 5 — TLS Handshake (HTTPS only)

### Step 5.1 — Browser sends ClientHello

The browser sends:
- Supported TLS versions
- Encryption methods
- Random value
- Domain name

---

### Step 5.2 — Server responds

The server sends:
- Selected TLS version
- Encryption method
- Certificate (contains public key)

---

### Step 5.3 — Browser verifies server

The browser checks:
- Certificate Authority (CA) trust
- Domain match
- Expiration

If invalid:
→ Connection stopped

---

### Step 5.4 — Secret exchange

The browser:
- Generates shared_secret
- Encrypts it using server public key
- Sends to server

The server:
- Decrypts using private key

Now:
→ Both share the same secret

---

### Step 5.5 — Secure channel established

Both sides:
- Derive symmetric keys
- Start encrypted communication

---

## 2.7 Step 6 — HTTP Request

### HTTP case

Browser sends:
GET / HTTP/1.1  
Host: example.com  

Network sees:
→ Plain text

---

### HTTPS case

Browser sends:
GET / HTTP/1.1  
Host: example.com  
Authorization: Bearer abc123  

Network sees:
→ Encrypted data

---

## 2.8 Example — Login Request

### Without HTTPS (HTTP)

Request:
POST /login  
username=admin&password=123  

Attacker sees:
- username
- password

---

### With HTTPS

Same request:

Attacker sees:
→ Encrypted data only

---

## 2.9 Full Flow Comparison

### HTTP

User → Browser  
↓  
DNS → IP  
↓  
TCP connection  
↓  
HTTP request (plain text)  
↓  
Response (plain text)  

---

### HTTPS

User → Browser  
↓  
DNS → IP  
↓  
TCP connection  
↓  
TLS handshake  
  → verify server  
  → exchange secret  
↓  
Secure channel  
↓  
HTTP request (encrypted)  
↓  
Response (encrypted)  

---

## 2.10 Key Takeaways

- Browser decides flow based on scheme (HTTP vs HTTPS)
- DNS works the same for both
- TCP connection is required for both
- TLS exists only in HTTPS
- HTTP sends readable data
- HTTPS encrypts all communication
- Middleman cannot read or modify HTTPS traffic