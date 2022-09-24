# XSS
TLDR: User inputs behaving as JS code
Prevention: Sanitize inputs

# Improper logout
Invalidate cookies, session IDs, auth tokens etc.  
```java
// For general session auth, invalidate the active session
request.getSession(false).invalidate();
```