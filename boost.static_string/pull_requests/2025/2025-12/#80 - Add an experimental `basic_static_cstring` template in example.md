# #80 Add an experimental `basic_static_cstring` template in example/ [Merged]

> Username: gennaroprota  
> Created at: 2025-12-19 17:37:18 UTC  
> Updated at: 2025-12-19 18:32:40 UTC  
> Merged at: 2025-12-19 18:32:30 UTC  
> Closed at: 2025-12-19 18:32:30 UTC  
> Url: https://github.com/boostorg/static_string/pull/80  

This introduces an alternative to `basic_static_string` designed for use in POD types: Trivially copyable, having a sizeof == N + 1, with no embedded NULs.  
  
Placed in example/ to gather user feedback before committing to a public API. See issue #23.

---
