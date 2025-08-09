**JWT** stands for JSON Web Token — it’s a compact, URL-safe way to represent claims securely between two parties (often a client and a server).

### 1. What it is
A JWT is basically a string with three parts separated by dots:

```ts
xxxxx.yyyyy.zzzzz
```

- Header → describes the token type (JWT) and the signing algorithm (e.g., HS256).

- Payload → contains claims (data) about the user or entity, e.g., {"userId": 123, "role": "admin"}.

- Signature → used to verify the token hasn’t been altered, created using the header, payload, and a secret key (or private key in asymmetric encryption).

### 2. How it works in authentication
- User logs in → sends credentials to the server.
- Server validates credentials and generates a JWT containing user info (claims).
- JWT sent to client → usually stored in localStorage, sessionStorage, or cookies.
- Client sends JWT in the Authorization header for each request:

```ts
Authorization: Bearer <token>
```
- Server verifies JWT using the secret key — if valid, processes the request; if not, rejects it.

### 3. Benefits
- Stateless → server doesn’t need to store sessions; token itself contains the data.

- Compact → small size, easily sent in HTTP headers.

- Cross-platform → works with any programming language.

### 4. Risks
- If stolen, anyone can use it until it expires.
- Cannot be invalidated easily before expiry without additional server logic (like a blacklist).
- Should not store sensitive data (like passwords) in the payload, because it’s only base64-encoded, not encrypted.

### How the traditional session + cookie approach works
- User logs in → sends credentials to server.

- Server creates a session in its **memory** or **database**, storing user info there.
- Server sends a session ID to the client in a cookie (e.g., sessionId=abc123).
- Client sends the cookie with every request.
- Server looks up the session ID in storage → retrieves user data → processes request.

**Key point:** The server **must store** session data somewhere (RAM, Redis, DB).

| Feature              | Session/Cookie                                           | JWT                                                                    |
| -------------------- | -------------------------------------------------------- | ---------------------------------------------------------------------- |
| **Storage**          | Server stores session data                               | No server storage (stateless) — all info in token                      |
| **Scalability**      | Harder to scale — each server must share session storage | Easier to scale — any server can verify JWT with the same secret key   |
| **Authentication**   | Cookie-based (browser-friendly)                          | Usually sent in `Authorization` header (can work on web, mobile, APIs) |
| **Performance**      | Requires DB/memory lookup each request                   | No lookup needed — verify token directly                               |
| **Invalidation**     | Easy — delete session on server                          | Hard — must track blacklists or change signing key                     |
| **Security**         | Protected by HTTPS, cookie flags (HttpOnly, Secure)      | Protected by HTTPS, signature verification                             |
| **Cross-domain use** | Limited — cookies tied to domains                        | Works across domains (good for APIs & microservices)                   |


### When to use which
Session/Cookie → Best for classic web apps where the client is always a browser, and you can store data on the server.

JWT → Best for APIs, mobile apps, microservices, or when you need stateless, distributed authentication.

### Simple Example
```ts
const jwt = require('jsonwebtoken');

// Your secret key (keep it safe!)
const SECRET_KEY = 'my_super_secret_key';

// 1️⃣ CREATE a token
function createToken() {
  const payload = {
    userId: 123,
    role: 'admin'
  };

  // Sign token with expiration
  const token = jwt.sign(payload, SECRET_KEY, { expiresIn: '1h' });
  console.log('Generated JWT:', token);
  return token;
}

// 2️⃣ VERIFY a token
function verifyToken(token) {
  try {
    const decoded = jwt.verify(token, SECRET_KEY);
    console.log('Token is valid. Payload:', decoded);
  } catch (err) {
    console.error('Token is invalid or expired:', err.message);
  }
}

// Example usage
const token = createToken();
verifyToken(token); // Should be valid

// Example of invalid token check
verifyToken(token + 'broken'); // Will throw error

```
