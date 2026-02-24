# #14 feature/no new line warning in c++11 [Merged]

> Username: sabel83  
> Created at: 2017-09-16 20:53:42 UTC  
> Updated at: 2017-09-16 20:58:16 UTC  
> Merged at: 2017-09-16 20:58:16 UTC  
> Closed at: 2017-09-16 20:58:16 UTC  
> Url: https://github.com/boostorg/wave/pull/14  

Remove the warning about the missing new line at the end of the source files in C++11 mode, as it is no longer expected.  
  
This is required to process some of the libcxx headers (and a number of other C++11 and above headers) with Wave without warnings.

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2017-09-16 20:58:11 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/14#pullrequestreview-63211891  

LGTM, thanks a lot!

---
