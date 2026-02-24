# #583 Classic.Tests: escape_char_parser_tests: Avoid overflow [Merged]

> Username: Kojoley  
> Created at: 2020-04-01 23:17:48 UTC  
> Updated at: 2020-04-02 18:21:49 UTC  
> Merged at: 2020-04-02 18:21:47 UTC  
> Closed at: 2020-04-02 18:21:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/583  

On ARM `ULONG_MAX == WCHAR_MAX`. Fixes #582

---
