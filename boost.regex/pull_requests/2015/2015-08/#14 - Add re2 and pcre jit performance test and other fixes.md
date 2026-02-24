# #14 Add re2 and pcre jit performance test and other fixes [Closed]

> Username: boytm  
> Created at: 2015-08-13 10:09:58 UTC  
> Updated at: 2015-08-19 03:36:09 UTC  
> Closed at: 2015-08-19 03:36:09 UTC  
> Url: https://github.com/boostorg/regex/pull/14  

- Add Google RE2 and pcre jit mode performance test .  
- Disable some test items that cannot obtain exptected result by posix library.  Posix extended lack non greedy repeats and escape character, eg "\n" .  
- Updated gcc-performance.html for CentOS 6.4.

---
