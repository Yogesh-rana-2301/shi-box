# JWT (JSON Web Token)

## 🔹 Introduction to JWT

**JWT** stands for **JSON Web Token**.

- It's an **open standard (RFC 7519)** that defines a secure way of transmitting information between parties as a JSON object.
- This information is **digitally signed**, so the receiver can **verify it's genuine** and hasn't been tampered with.
- JWTs are mainly used for:
  - **Authentication** (logging in users)
  - **Authorization** (controlling access to resources)

## 🔹 Authentication vs Authorization

Understanding the difference is crucial:

### 1. **Authentication** → _Who are you?_

- Example: Logging in with username & password
- Confirms your identity
- Think of it like showing your ID at a club entrance

### 2. **Authorization** → _What are you allowed to do?_

- Example: Once logged in, can you access `/admin`? Or only `/profile`?
- Decides what resources you can use
- Like having different access levels in a building (employee vs manager vs janitor)

Traditional systems used **sessions stored on the server**, but JWT changes this flow.

## 🔹 Traditional Session-Based Authentication

Here's how it normally worked before JWT:

1. User enters username + password
2. Server verifies credentials and stores the user's data in **server memory** (a session)
3. Server creates a **session ID** and sends it back to the client in a cookie
4. For every future request:
   - Browser sends session ID via cookie
   - Server looks up the session ID in memory and checks who the user is

### **Problem:**

- Server must **store sessions for every logged-in user**
- Doesn't scale well for **multiple servers or microservices**
- If server crashes, all sessions are lost
- Hard to share authentication across different applications

## 🔹 JWT Authentication Process

With JWT, the flow is different:

1. User enters username + password
2. Server verifies credentials
3. Instead of creating a session, the server **creates a JWT**:
   - Encodes user info into it
   - Signs it with a **secret key**
4. JWT is sent back to the client
   - Client stores it (in **localStorage**, sessionStorage, or cookies)
5. For every future request:
   - Client sends the JWT (usually in the **Authorization header** as `Bearer <token>`)
   - Server verifies the JWT using the secret key

✅ **No need to store sessions in memory. The token itself contains the info.**

## 🔹 Structure of a JWT

A JWT has **3 parts**, separated by dots (`.`):

```
xxxxx.yyyyy.zzzzz
```

### 1. **Header**

JSON object that specifies the signing algorithm & token type.

**Example:**

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

- Encoded in **Base64**
- `alg`: Algorithm used for signing
- `typ`: Type of token (always "JWT")

### 2. **Payload**

Contains **claims** → user info & metadata.

**Example:**

```json
{
  "sub": "1234567890",
  "name": "Yaar",
  "iat": 1516239022,
  "exp": 1516242622,
  "role": "admin"
}
```

**Common Claims:**

- `sub` → Subject (user ID)
- `iat` → Issued at (timestamp)
- `exp` → Expiration time (timestamp)
- `iss` → Issuer (who created the token)
- `aud` → Audience (who should receive it)

### 3. **Signature**

Protects the token from tampering.

**Created by hashing:**

```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret
)
```

👉 **You can decode a JWT at [jwt.io](https://jwt.io) and see the payload & header.**

## 🔹 Verification of JWT

When a request comes with a JWT:

1. Server decodes the header & payload
2. Server recalculates the signature using the same secret key
3. If the signature matches → ✅ token is valid
4. If not → ❌ token has been modified (reject request)
5. Check if token has expired (`exp` claim)

This ensures **trust** without storing user data server-side.

## 🔹 Advantages of JWT

### 1. **Scalability**

- Works great with **multiple servers & microservices** because no session storage is needed
- Any server with the **secret key** can verify the token
- Perfect for load-balanced environments

### 2. **Stateless Authentication**

- All info is stored in the token
- Server doesn't need to track users in memory
- Reduces database lookups

### 3. **Cross-Service Authorization**

- Example: A **banking app** and a **retirement app** both trust the same JWT signed by the bank's server
- Users log in once and can use both apps without logging in again
- Great for Single Sign-On (SSO)

### 4. **Mobile-Friendly**

- Easy to store and send from mobile apps
- No need to manage cookies

## 🔹 Disadvantages of JWT

### 1. **Token Size**

- JWTs are larger than session IDs
- More data transferred with each request

### 2. **Token Revocation**

- Hard to "logout" users immediately since tokens are stateless
- Once issued, valid until expiration (unless you maintain a blacklist)

### 3. **Security Concerns**

- If someone steals your JWT, they can impersonate you until it expires
- Sensitive data in payload is visible (though signed)

## 🔹 Best Practices for JWT

### **Storage:**

- **Don't store in localStorage** if you can avoid it (XSS attacks)
- **httpOnly cookies** are safer for web apps
- Use **secure** and **sameSite** cookie flags

### **Security:**

- Use strong secret keys
- Set reasonable expiration times (15-30 minutes for access tokens)
- Use refresh tokens for longer sessions
- Always validate tokens on the server
- Don't put sensitive data in the payload

### **Implementation:**

- Use HTTPS always
- Implement proper error handling
- Consider rate limiting for token endpoints

## 🔹 JWT vs Sessions Comparison

| Feature                | JWT                             | Sessions           |
| ---------------------- | ------------------------------- | ------------------ |
| **Storage**            | Client-side                     | Server-side        |
| **Scalability**        | Excellent                       | Limited            |
| **Security**           | Good (if implemented correctly) | Good               |
| **Performance**        | Less server memory              | More server memory |
| **Logout**             | Difficult                       | Easy               |
| **Token Size**         | Larger                          | Smaller            |
| **Offline Capability** | Yes                             | No                 |

## 🔹 When to Use JWT

### **✅ Good for:**

- **APIs, microservices, distributed systems**
- **Mobile applications**
- **Single Page Applications (SPAs)**
- **Cross-domain authentication**
- **Scalable, stateless authentication**
- **Third-party integrations**

### **❌ Not ideal for:**

- Simple, single-server applications (where sessions work fine)
- Applications requiring immediate logout capability
- High-security applications with frequent permission changes

## 🔹 Real-World Example

### **Login Flow:**

```
1. POST /login { username: "john", password: "secret123" }
2. Server validates → Creates JWT
3. Response: { "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..." }
4. Client stores token
```

### **Accessing Protected Routes:**

```
GET /profile
Headers: {
  "Authorization": "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

## 🔹 Code Examples

### **JavaScript (Frontend):**

```javascript
// Storing JWT after login
localStorage.setItem("token", response.data.token);

// Sending JWT with requests
const token = localStorage.getItem("token");
axios.get("/api/profile", {
  headers: {
    Authorization: `Bearer ${token}`,
  },
});
```

### **Node.js (Backend):**

```javascript
const jwt = require("jsonwebtoken");

// Creating JWT
const token = jwt.sign(
  { userId: user.id, role: user.role },
  "your-secret-key",
  { expiresIn: "1h" }
);

// Verifying JWT
const decoded = jwt.verify(token, "your-secret-key");
```

### **Express.js Middleware:**

```javascript
function authenticateToken(req, res, next) {
  const authHeader = req.headers["authorization"];
  const token = authHeader && authHeader.split(" ")[1];

  if (!token) {
    return res.sendStatus(401);
  }

  jwt.verify(token, process.env.ACCESS_TOKEN_SECRET, (err, user) => {
    if (err) return res.sendStatus(403);
    req.user = user;
    next();
  });
}
```

## 🔹 Common Mistakes to Avoid

1. **Storing sensitive data in JWT payload** (it's visible to anyone)
2. **Using weak secret keys** (use long, random strings)
3. **Not setting expiration times** (tokens live forever)
4. **Storing JWTs in localStorage** without considering XSS
5. **Not validating tokens properly** on the server
6. **Forgetting to handle token expiration** on the frontend

## 🔹 Refresh Token Pattern

For better security, use **Access + Refresh Token** pattern:

- **Access Token**: Short-lived (15-30 minutes), used for API requests
- **Refresh Token**: Long-lived (days/weeks), used to get new access tokens

```javascript
// When access token expires:
1. Send refresh token to /refresh endpoint
2. Server validates refresh token
3. Server sends new access token
4. Client updates stored token
```

## 🔹 Tools and Resources

- **[jwt.io](https://jwt.io)** - Decode and verify JWTs
- **Libraries:**
  - Node.js: `jsonwebtoken`
  - Python: `PyJWT`
  - Java: `java-jwt`
  - PHP: `firebase/php-jwt`

## 🔹 Summary

JWT is a powerful tool for modern web authentication that offers:

- **Scalability** for distributed systems
- **Stateless** authentication
- **Cross-service** compatibility
