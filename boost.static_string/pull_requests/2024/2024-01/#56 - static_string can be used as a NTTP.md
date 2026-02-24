# #56 static_string can be used as a NTTP [Closed]

> Username: sdkrystian  
> Created at: 2024-01-10 12:35:16 UTC  
> Updated at: 2025-12-16 10:11:43 UTC  
> Closed at: 2025-12-16 10:11:42 UTC  
> Url: https://github.com/boostorg/static_string/pull/56  

(related: #55)  
  
@pdimov I ended up not using unnamed classes because clang required that they be "C compatible". Instead, I'm using the injected-class-name (which results in the same behavior).

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-01-10 15:20:08 UTC  
> Url: https://github.com/boostorg/static_string/pull/56#issuecomment-1885050075  

We'd need a test that it can be used as an NTTP, though. :-)

---

## Comment 2

> Username: gennaroprota  
> Created_at: 2025-12-16 10:11:42 UTC  
> Url: https://github.com/boostorg/static_string/pull/56#issuecomment-3659777204  

Closing this and opening a new PR, to fold the last three commits.

---
