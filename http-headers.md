## HTTP Headers
HTTP headers are metadata sent with an HTTP request or response to provide context about the message, client, server, and content. 
They are key-value pairs that appear before the message body and include information like the content type, cache directives, and authentication credentials.

#### What they are
* **Metadata for context:** Headers carry additional information that isn't part of the actual message body, helping the client and server understand the request and response more fully.
* **Key-value pairs:** Each header consists of a name, followed by a colon, and then its value (e.g., `Content-Type: application/json`).
*  **Placement:** Headers are placed between the request/response line and the message body.

### 📌 HTTP Message Structure
HTTP headers are placed between the start-line and the message body in both HTTP requests and responses. 
This structure is required by the HTTP specification and ensures that metadata about the message is known before the actual content is processed.
HTTP Request Format
```ts
<request-line>
<headers>
<blank line>
<optional body>
```

Example
```vbnet
GET /users HTTP/1.1
Host: example.com
Accept: application/json
// (Blank line indicates end of headers.)
```

HTTP Response Format
```ts
<status-line>
<headers>
<blank line>
<optional body>
```

Example
```ts
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 27

{"message": "success"}

```

### HTTP Header Groups
HTTP headers can be logically grouped based on their purpose and where they are used. These groups help developers understand **why** a header exists and **when** it should be used.

# General Headers
**General headers** can appear in both requests and responses.
They provide information that applies to the message as a whole, not specifically to the body or to request/response semantics.

### **Examples**

- **Cache-Control**

Controls caching behavior for both clients and intermediaries.  
Specifies rules like `no-cache`, `no-store`, `max-age`, `public`, or `private`.

- **Connection**

Controls whether the network connection stays open after the request.  
Common values: `keep-alive` (reuse connection), `close`.

- **Date**

The date and time when the HTTP message was created by the sender.  
Used for caching, debugging, and synchronization.

- **Pragma**

Legacy HTTP/1.0 header for cache control.  
Commonly seen as: `Pragma: no-cache` (still used for backward compatibility).

- **Via**

Shows intermediate proxies or gateways through which the message passed.  
Useful for tracking routing, debugging, and enforcing proxy policies.

- **Transfer-Encoding**

Indicates how the message body is encoded for transfer.  
Most common value: `chunked` (used when the body size is unknown at the start).

# Request Headers
Headers sent **only by the client** in an HTTP request.
They provide information about the client, requested resource, preferred formats, or authentication credentials.

- **Host**
  - Specifies the domain name and optional port of the server being requested. Required in HTTP/1.1.
  - **Use Case:** Virtual hosting

- **User-Agent**
  - Identifies the client software (browser, app, bot) making the request.
  - **Use Cases:** Analytics, debugging, content adaptation

- **Accept**
  - Indicates the media types the client can process (e.g., `application/json`, `text/html`).
  - **Use Case:** Content negotiation

- **Accept-Encoding**
  - Specifies which content encodings the client supports (e.g., `gzip`, `br`).
  - **Use Case:** Compressed responses

- **Accept-Language**
  - Indicates the user's preferred languages (e.g., `en-US`, `fr`).
  - **Use Case:** Localized content

- **Authorization**
  - Contains credentials for authentication (e.g., `Bearer <token>`, `Basic <base64>`).
  - **Use Case:** Authentication

- **Cookie**
  - Sends stored cookies back to the server.
  - **Use Cases:** Sessions, user state, authentication

- **Referer**
  - Indicates the URL of the page that made the request. (Spec spells it this way.)
  - **Use Cases:** Analytics, CSRF protection

- **If-Modified-Since**
  - Conditionally fetches the resource only if it was modified after the given date.
  - **Use Case:** Efficient caching

- **If-None-Match**
  - Conditionally fetches the resource only if the ETag does *not* match; server may return `304 Not Modified`.
  - **Use Case:** Efficient caching

- **Content-Type**
  - Describes the media type of the request body (e.g., `application/json`, `multipart/form-data`).
  - **Use Case:** Sending data to the server

- **Content-Length**
  - Indicates the size (in bytes) of the request body.
  - **Use Case:** Required for some POST/PUT requests

- **Origin**
  - Indicates the origin (scheme + host + port) of the request.
  - **Use Cases:** CORS, CSRF mitigations

- **Accept-Charset**
  - Specifies preferred character sets (rarely used in modern browsers).
  - **Use Case:** Character set handling

- **Range**
  - Requests a partial resource (e.g., `bytes 0–500`).
  - **Use Cases:** Resumable downloads, streaming

- **DNT**
  - Indicates user's tracking preference (`1 = do not track`), though widely ignored.
  - **Use Case:** Privacy preference

- **TE**
  - Indicates acceptable transfer encodings (different from content encodings).
  - **Use Case:** Transfer encoding

 # Response Headers
 HTTP response headers are key-value pairs sent by the server in response to a client’s HTTP request. 
 They provide metadata about the response, instructions to the client, or information about the server itself. Unlike request headers (which describe the client and what it wants), response headers describe the status and content of the server’s response.
 

- **Server**  
  Identifies the server software handling the request.  
  Example: `Server: Apache/2.4.41 (Ubuntu)`  
  Used for debugging, analytics, and sometimes fingerprinting.

- **Set-Cookie**  
  Sends cookies from server → client.  
  Supports flags like `HttpOnly`, `Secure`, `SameSite`.

- **Location**  
  Specifies the URL to redirect the client to (used with 3xx status codes).  
  Example: `Location: https://example.com/login`

- **ETag**  
  Unique identifier for a resource version.  
  Used for caching and conditional requests (`If-None-Match`).

- **WWW-Authenticate**  
  Indicates authentication method required by the server.  
  Used in 401 responses (e.g., Basic, Digest, Bearer).

- **Retry-After**  
  Tells the client how long to wait before retrying a request.  
  Used in 429 (Too Many Requests) or 503 (Service Unavailable) responses.

- **Content-Type**  
  Describes the media type of the response body.  
  Example: `application/json`, `text/html`.

- **Content-Length**  
  The size of the response body in bytes.  
  Used by clients to know how much data to expect.

- **Content-Encoding**  
  Indicates compression applied to the response body (e.g., `gzip`, `br`).  
  Client must decode before use.

- **Content-Language**  
  Specifies the natural language of the response (e.g., `en-US`).  
  Used for localization and language negotiation.

- **Last-Modified**  
  Timestamp when the resource was last modified.  
  Used with caching and conditional requests (`If-Modified-Since`).

- **Cache-Control**  
  Controls caching behavior for the resource.  
  Examples: `no-cache`, `private`, `max-age=3600`.

- **Expires**  
  Specifies absolute expiration date for cached resource.  
  Superseded by `Cache-Control` but still supported.

- **Vary**  
  Indicates which request headers were used to select the response.  
  Example: `Vary: Accept-Encoding` (important for caching proxies).

- **Age**  
  Time in seconds since the response was generated or revalidated by a cache.  
  Used to track freshness in caches/CDNs.

Example
```ts
HTTP/1.1 200 OK
Date: Wed, 04 Dec 2025 12:00:00 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Type: text/html; charset=UTF-8
Content-Length: 305
Connection: keep-alive
Cache-Control: max-age=3600
Set-Cookie: sessionId=abc123; HttpOnly; Secure

```

# Representation
These headers describe the body/content of the message (request or response). They tell the client or server how to interpret the message body.
-   **Content-Type** – Specifies the media type of the body (e.g., `application/json`, `text/html`).
    
-   **Content-Length** – Size of the body in bytes; helps the recipient know how much data to read.
    
-   **Content-Encoding** – Indicates compression or transformation applied to the body (e.g., `gzip`, `br`).
    
-   **Content-Language** – Specifies the natural language of the body (e.g., `en-US`, `fr`).
    
-   **Content-Location** – Optional URI for the resource contained in the body; can differ from the request URI.
    
-   **Content-Disposition** – Suggests how the content should be handled (`inline` vs `attachment`) and optionally sets a filename.
    
-   **Content-Range** – Indicates the byte range being sent in a partial response (used with `Range` requests).
    
-   **Last-Modified** – Timestamp of when the resource was last changed; useful for caching and conditional requests.
    
-   **ETag** – Unique identifier for the resource version; used with `If-None-Match` for conditional requests.
    
-   **Expires** – Absolute date/time when the content is considered stale; often replaced by `Cache-Control`.

# Security Headers
These headers enhance the security of web applications by controlling browser behavior, enforcing policies, and protecting against attacks like XSS, clickjacking, and man-in-the-middle (MITM).
They are primarily response headers, though some may also affect requests.

-   **Strict-Transport-Security (HSTS)** – Forces browsers to use HTTPS for all future requests to the domain for a specified time.
    
-   **Content-Security-Policy (CSP)** – Defines which resources (scripts, images, etc.) are allowed to load; protects against XSS attacks.
    
-   **X-Frame-Options** – Prevents the page from being embedded in frames or iframes to mitigate clickjacking (`DENY`, `SAMEORIGIN`).
    
-   **X-XSS-Protection** – Enables or disables the browser’s built-in XSS protection (deprecated in modern browsers).
    
-   **X-Content-Type-Options** – Prevents MIME type sniffing; common value: `nosniff`.
    
-   **Referrer-Policy** – Controls how much referrer information is sent with requests (`no-referrer`, `strict-origin-when-cross-origin`).

# CORS (Cross-Origin Resource Sharing) Headers
These headers enable controlled cross-origin access to resources. They allow servers to specify which external domains, HTTP methods, and headers are allowed when accessing resources.

-   **Access-Control-Allow-Origin** – Specifies which origin(s) are allowed to access the resource.
    
-   **Access-Control-Allow-Methods** – Lists the HTTP methods allowed for cross-origin requests (`GET`, `POST`, `PUT`, etc.).
    
-   **Access-Control-Allow-Headers** – Lists the request headers the server allows for cross-origin requests.
    
-   **Access-Control-Allow-Credentials** – Indicates whether cookies or authentication information can be included in cross-origin requests.
    
-   **Access-Control-Expose-Headers** – Specifies which response headers are safe to expose to the client.
    
-   **Access-Control-Max-Age** – Specifies how long the results of a preflight request can be cached (in seconds).

##  Headers Controlled by the Browser
Some HTTP headers are managed automatically by the browser and cannot be fully modified by JavaScript or even by the server in some cases. These are often related to security or network behavior. Examples:
| Header             | Who Controls          | Notes                                                                                            |
| ------------------ | --------------------- | ------------------------------------------------------------------------------------------------ |
| **Host**           | Browser / TCP stack   | Set automatically from URL; server cannot override it.                                           |
| **Origin**         | Browser               | Automatically set for cross-origin requests; JS cannot modify it for security reasons.           |
| **Referer**        | Browser               | JS can’t arbitrarily change it (except limited with `<a rel="noreferrer">` or `referrerPolicy`). |
| **User-Agent**     | Browser               | Some browsers let you spoof it via dev tools, but JS cannot modify it in requests.               |
| **Content-Length** | Browser / HTTP client | Calculated automatically from the body; you cannot set it from JS.                               |
| **Connection**     | Browser / TCP         | Managed by the network stack; cannot be reliably changed.                                        |
| **Cookie**         | Partly modifiable     | JS can only modify non-`HttpOnly` cookies; `HttpOnly` cookies are not accessible via JS.         |



