# #120 Fix doc warnings [Merged]

> Username: Lastique  
> Created at: 2024-01-02 15:29:55 UTC  
> Updated at: 2024-01-02 16:48:09 UTC  
> Merged at: 2024-01-02 16:42:59 UTC  
> Closed at: 2024-01-02 16:42:59 UTC  
> Url: https://github.com/boostorg/optional/pull/120  

A few commits that fix a few warnings produced when building docs:  
  
- `b2` warning about using reserved characters in parameter values  
- QuickBook warnings about invalid characters  
- QuickBook warnings about line breaks potentially producing invalid Boost.Book output. Also fixes broken output for IO operator reference.  
  
None of these are "fatal" errors, i.e. the docs are building successfully even with the warnings. But worth fixing anyway.

---
