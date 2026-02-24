# #5 new narrow and widen overloads for input string count [Closed]

> Username: adzm  
> Created at: 2016-08-02 16:51:02 UTC  
> Updated at: 2020-02-10 14:08:37 UTC  
> Closed at: 2020-02-10 14:08:37 UTC  
> Url: https://github.com/boostorg/nowide/pull/5  

New narrow and widen overloads accept count of input string and returns std::string or std::wstring.  
  
Useful for situations where you might not have a null-terminated string, but don't want to wrap it in a std::string or std::wstring

---

## Comment 1

> Username: Flamefire  
> Created_at: 2020-02-10 14:08:36 UTC  
> Url: https://github.com/boostorg/nowide/pull/5#issuecomment-584139317  

Implemented in #66. Thanks for the suggestion!

---
