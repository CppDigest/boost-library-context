# #49 Remove unneeded convert from wchar_t to char on POSIX environment [Merged]

> Username: phprus  
> Created at: 2021-02-11 15:36:30 UTC  
> Updated at: 2021-03-01 09:55:40 UTC  
> Merged at: 2021-03-01 09:55:31 UTC  
> Closed at: 2021-03-01 09:55:31 UTC  
> Url: https://github.com/boostorg/dll/pull/49  

On POSIX environment ``fs::path::value_type`` is ``char`` (https://isocpp.org/files/papers/P0218r1.html#value_type, https://en.cppreference.com/w/cpp/filesystem/path).  
Using a ``wchar_t`` string and converting from ``wchar_t`` to ``char`` is not required.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2021-03-01 09:55:40 UTC  
> Url: https://github.com/boostorg/dll/pull/49#issuecomment-787818869  

Many thanks for the fix!

---
