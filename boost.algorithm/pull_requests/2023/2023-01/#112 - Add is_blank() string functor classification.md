# #112 Add is_blank() string functor classification [Merged]

> Username: glywk  
> Created at: 2023-01-20 23:54:06 UTC  
> Updated at: 2025-04-13 20:46:42 UTC  
> Merged at: 2025-04-13 20:46:42 UTC  
> Closed at: 2025-04-13 20:46:42 UTC  
> Url: https://github.com/boostorg/algorithm/pull/112  

In string algorithm, is_blank standard classification exists since C++11. Today, this classification is not provided by this library for code.  
I have added is_blank functor in the implementation compiled with standard upper or equals to c++11 and complete the documentation.

---

## Comment 1

> Username: glywk  
> Created_at: 2023-07-26 23:37:54 UTC  
> Url: https://github.com/boostorg/algorithm/pull/112#issuecomment-1652686444  

@mclow

---

## Comment 2

> Username: glywk  
> Created_at: 2023-07-27 21:46:09 UTC  
> Url: https://github.com/boostorg/algorithm/pull/112#issuecomment-1654627373  

@mclow as suggested, I have add a negative is_blank() predicate test case. It checks that:  
- first, any of chars are not blanks. `'\n','\v','\f','\r'` are classified as spaces characters as describe in [std::isblank](https://en.cppreference.com/w/cpp/string/byte/isblank)  
- second, not all chars are blanks. (ie. `'a','x'`)

---

## Comment 3

> Username: glywk  
> Created_at: 2023-11-10 00:21:20 UTC  
> Url: https://github.com/boostorg/algorithm/pull/112#issuecomment-1804880937  

Is something miss?

---
