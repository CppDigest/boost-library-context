# #69 Extend GCC and clang cxxflags for detailed warnings [Merged]

> Username: mloskot  
> Created at: 2018-03-31 21:39:39 UTC  
> Updated at: 2018-04-01 12:11:07 UTC  
> Merged at: 2018-04-01 12:11:03 UTC  
> Closed at: 2018-04-01 12:11:03 UTC  
> Url: https://github.com/boostorg/gil/pull/69  

### Description  
  
- Group compilation flags and defines in common top-level Jamfile - relies on Boost.Build feature of referring parent Jamfile-s.  
- Bump MSVC warning level to W4  
- Preparing for detailed warnings clean up based on:  
https://svn.boost.org/trac10/wiki/Guidelines/WarningsGuidelines  
  
## Tasklist  
- [x] All CI builds and checks have passed  
  
# References  
  
I'm hopeful it will also help to investigate #49

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-04-01 12:10:56 UTC  
> Url: https://github.com/boostorg/gil/pull/69#issuecomment-377782396  

It should be non-intrusive refactoring with addition of extra flags. The CI builds look good. So I've decided to merge it.

---
