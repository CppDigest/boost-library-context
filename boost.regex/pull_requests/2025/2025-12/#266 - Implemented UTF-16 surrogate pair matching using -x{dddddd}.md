# #266 Implemented UTF-16 surrogate pair matching using \x{dddddd}. [Open]

> Username: da40  
> Created at: 2025-12-27 16:57:00 UTC  
> Updated at: 2025-12-27 16:57:00 UTC  
> Url: https://github.com/boostorg/regex/pull/266  

It was not possible to search for UTF-16 characters that had to be represented as surrogate pairs (e.g. 😑 occupies 4 bytes).  This amendment converts a UTF-32 code point entered as \x{dddddd} to its equivalent UTF-16 surrogate pair.

---
