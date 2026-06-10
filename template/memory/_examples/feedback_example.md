---
name: feedback_validation_patterns
description: Always validate input on both client and server side, never trust client-only validation.
metadata:
  type: feedback
---

Always validate input on both client AND server side.

**Why:** Client-only validation is trivially bypassed. We had a security incident in sprint 5 where a user submitted malformed data directly to the API, bypassing frontend checks.

**How to apply:** Every endpoint must independently validate all inputs. Frontend validation is for UX only. Backend validation is the security boundary.
