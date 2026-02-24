# #61 Makes enum_to_string constexpr - refs #60 [Closed]

> Username: Julien-Blanc-tgcm  
> Created at: 2025-12-11 13:18:27 UTC  
> Updated at: 2025-12-15 11:09:49 UTC  
> Closed at: 2025-12-15 11:09:48 UTC  
> Url: https://github.com/boostorg/describe/pull/61  

* Can be constexpr when c++ > 17

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-12-11 13:35:13 UTC  
> Url: https://github.com/boostorg/describe/pull/61#issuecomment-3641967990  

What part requires C++17?

---

## Comment 2

> Username: Julien-Blanc-tgcm  
> Created_at: 2025-12-15 08:59:01 UTC  
> Url: https://github.com/boostorg/describe/pull/61#issuecomment-3654507610  

Well, that's what gcc tolds me. Trying to compile with C++14, i get   
  
```  
cc1plus: note:   ‘enum_to_string<E>(E, const char*)::<lambda(auto:10)>’ is a closure type, which is only literal in C++17 and later  
```  
  
I vaguely remember that lambdas could not be used in constexpr in C++14, i never had these rules right and i mostly target C++20 now, so i'll trust gcc on this.

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-12-15 09:37:37 UTC  
> Url: https://github.com/boostorg/describe/pull/61#issuecomment-3654677318  

Ah, the lambda. Well, this is fixable.

---

## Comment 4

> Username: pdimov  
> Created_at: 2025-12-15 11:09:48 UTC  
> Url: https://github.com/boostorg/describe/pull/61#issuecomment-3655058801  

Implemented on develop.

---
