# #15 Add re2 and pcre jit test and fix posix regex bugs [Closed]

> Username: boytm  
> Created at: 2015-08-19 03:47:52 UTC  
> Updated at: 2017-10-25 17:10:54 UTC  
> Closed at: 2017-10-25 17:10:53 UTC  
> Url: https://github.com/boostorg/regex/pull/15  

- Add Google re2 and pcre jit test .  
- Disable some test items that cannot obtain exptected result by posix regex library.  Posix extended lack non greedy repeats and escape character, eg "\n" .  
- Add gcc44-performance.html for CentOS 6.4.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-10-25 17:10:53 UTC  
> Url: https://github.com/boostorg/regex/pull/15#issuecomment-339401841  

This has now been done in a different fashion.

---
