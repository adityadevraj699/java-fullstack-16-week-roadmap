# Spring Security Notes
> Written like a Java dev thinks — plain, practical, no corporate fluff.

---

## 1. The Big Picture — Auth vs Authz

These two words look similar but do completely different jobs.

**Authentication** = "Who are you?" — This is your login check. You prove you are who you say you are by giving a username + password. Spring checks it. Done.

**Authorization** = "What can you do?" — This happens after login. Even if you're logged in, can you access `/admin`? Spring checks your role here.

Think of it like an office building:
- Authentication = showing your ID at the reception desk.
- Authorization = your keycard deciding which floors you can enter.

---

## 2. Authentication Flow — What Actually Happens at Login?

When the client sends `POST /login` with username + password, here's the exact sequence inside Spring:

```
Client
  → AuthController           (you write this — it's just a REST controller)
  → AuthenticationManager    (Spring's brain — you just call authenticate())
  → DaoAuthenticationProvider (Spring's built-in — handles the actual verification)
  → UserDetailsService       (you implement this — it fetches user from DB)
  → Database                 (1 SELECT query — gets the user row)
  → PasswordEncoder          (BCrypt — compares raw password vs stored hash)
  → Authentication object    (if match → create Authentication, store in context)
  → JWT token                (you generate this and return it to client)
```

### What you write vs what Spring writes

| Class | Who writes it | What it does |
|---|---|---|
| `AuthController` | You | REST endpoint that receives login request |
| `AuthenticationManager` | Spring | Delegates to the right provider |
| `UserDetailsService` | You | `loadUserByUsername()` — hits DB and returns `UserDetails` |
| `PasswordEncoder` | Spring (you configure) | BCrypt match — never store plain passwords |
| `JwtUtil` / `JwtService` | You | Generate + validate JWT |

> **Dev tip:** You never call `PasswordEncoder.matches()` yourself. Spring calls it internally when you call `authManager.authenticate()`. Just trust the process.

---

## 3. Request Flow — What Happens on Every API Call?

After login, the client keeps the JWT and sends it in every request header: `Authorization: Bearer <token>`. Here's what Spring does for each request:

```
Client (sends Bearer token)
  → JwtFilter                (your custom filter — extends OncePerRequestFilter)
  → Parse JWT from header    (extract token string)
  → Validate token           (check signature, expiry, etc.)
     ├── INVALID → throw 401 / 403 → stop here
     └── VALID  → continue
  → SecurityContextHolder    (set the Authentication object here — Spring reads it)
  → SecurityConfig           (your config — checks if role allows this endpoint)
  → Controller               (finally reaches your actual business logic)
```

### Why `OncePerRequestFilter`?

Because without it, in some Servlet configurations, a filter could run twice for a single request. `OncePerRequestFilter` guarantees your JWT filter runs exactly once. Always extend it.

---

## 4. Key Classes — What Each One Actually Does

### `AuthenticationManager`
The entry point for login. You inject it in your `AuthController` and call:
```java
authManager.authenticate(
    new UsernamePasswordAuthenticationToken(username, password)
);
```
That's it. Spring handles the rest internally. If credentials are wrong, it throws `BadCredentialsException`.

### `UserDetailsService`
Your job is to implement this. Spring calls your `loadUserByUsername()` with the username from the login request. You hit the DB and return a `UserDetails` object. That's the only method you need to implement.

```java
@Override
public UserDetails loadUserByUsername(String username) {
    User user = userRepo.findByEmail(username)
        .orElseThrow(() -> new UsernameNotFoundException("Not found"));
    return new CustomUserDetails(user);
}
```

### `PasswordEncoder`
You configure BCrypt and Spring uses it automatically. Never call `.matches()` yourself during login — Spring calls it internally when comparing the raw password against the stored hash.

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}
```

### `SecurityContextHolder`
Think of this as Spring's "current logged-in user" bucket. It's thread-local — each request thread has its own. Once you set the `Authentication` object here inside `JwtFilter`, the rest of Spring's chain (and your controllers) can access the current user via:

```java
SecurityContextHolder.getContext().getAuthentication();
```

### `JwtFilter` (your custom class)
This is your most important custom class for stateless auth. It runs on every request before hitting your controllers.

```java
@Component
public class JwtFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(HttpServletRequest req, ...) {
        String token = extractTokenFromHeader(req);
        if (token != null && jwtUtil.isValid(token)) {
            String username = jwtUtil.extractUsername(token);
            UserDetails user = userDetailsService.loadUserByUsername(username);
            UsernamePasswordAuthenticationToken authToken =
                new UsernamePasswordAuthenticationToken(user, null, user.getAuthorities());
            SecurityContextHolder.getContext().setAuthentication(authToken);
        }
        filterChain.doFilter(req, res);
    }
}
```

---

## 5. JWT — Stateless Auth, The Developer's Friend

JWT = JSON Web Token. Three parts: `header.payload.signature`, all Base64 encoded.

**Why stateless?** The server stores nothing. The token itself contains the user info (username, roles, expiry). The server just validates the signature on each request.

**The Golden Rule of JWT:**
- Server signs with a secret key on login → generates token
- Server verifies with the same secret key on every request → validates token
- No DB call needed for validation (just signature check)

**How it's sent:**
```
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJ1c2VyQGVtYWlsLmNvbSJ9.SomeSignature
```

---

## 6. DB Calls — How Many? When?

| Flow | DB Calls | When |
|---|---|---|
| Login | 1 SELECT (fetch user) | Inside `loadUserByUsername()` |
| Login (optional) | 1 UPDATE (last login time) | After successful auth, your choice |
| Every API request | 0 (ideally) | JWT validation is just signature check |

> **Watch out:** A common mistake is calling `loadUserByUsername()` twice during request flow — once in `JwtFilter` and once somewhere else in the chain. That doubles your DB hits for no reason. Cache it or avoid calling it twice.

---

## 7. Error Responses — 401 vs 403

| Code | Name | Meaning in Spring |
|---|---|---|
| `401` | Unauthorized | No valid token present — you're not authenticated |
| `403` | Forbidden | Token is valid, but your role doesn't allow this endpoint |

In Spring, you configure these in `SecurityConfig`:
```java
.exceptionHandling(ex -> ex
    .authenticationEntryPoint((req, res, e) -> res.sendError(401)) // no token
    .accessDeniedHandler((req, res, e) -> res.sendError(403))       // wrong role
)
```

---

## 8. Best Practices — Avoid These Common Mistakes

### Use `CustomUserDetails`, not Spring's default `User`
Spring's built-in `User` class only has username, password, and roles. You almost always need more (userId, email, etc.). Create your own:

```java
public class CustomUserDetails implements UserDetails {
    private User user; // your entity

    public Long getUserId() { return user.getId(); } // extra field!

    // implement the required methods...
}
```

### Don't call `loadUserByUsername()` twice in one request
Once in `JwtFilter` is enough. Set it in `SecurityContextHolder` and let everything downstream read from there.

### Always use BCrypt — Never MD5/SHA
BCrypt is slow by design. That's the point — it makes brute-force attacks expensive. SHA/MD5 are fast and unsafe for passwords.

### Register `JwtFilter` in `SecurityConfig`, not as a random `@Component` only
```java
http.addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class);
```
This places your filter at the right point in Spring's filter chain.

---

## 9. The Golden Line

> **"AuthenticationManager handles login. JwtFilter handles request authentication."**

Every time you get confused, come back to this. These are the two pivot points of the entire Spring Security flow. Everything else — `UserDetailsService`, `PasswordEncoder`, `SecurityContextHolder` — are just helpers that these two use internally.

---

## Quick Reference Card

```
LOGIN:
  Client → AuthController
         → authManager.authenticate()
         → UserDetailsService.loadUserByUsername()
         → DB (1 SELECT)
         → BCrypt.matches()
         → if OK → generate JWT → return

PER REQUEST:
  Client (+ Bearer token)
         → JwtFilter.doFilterInternal()
         → validate JWT signature
         → if OK → setAuthentication() in SecurityContextHolder
         → SecurityConfig checks role
         → Controller runs

ERRORS:
  401 = no valid token (not authenticated)
  403 = token valid but role blocked (not authorized)
```
