# #5 MSVC level 4 warning fixes [Closed]

> Username: sergiud  
> Created at: 2014-08-04 12:46:08 UTC  
> Updated at: 2014-08-18 14:36:26 UTC  
> Closed at: 2014-08-18 14:34:00 UTC  
> Url: https://github.com/boostorg/signals2/pull/5  

These changes fix two level 4 warnings issued by MSVC 12.0.

---

## Comment 1

> Username: fmhess  
> Created_at: 2014-08-15 21:19:24 UTC  
> Url: https://github.com/boostorg/signals2/pull/5#issuecomment-52359032  

I've fixed the unused parameter warning, but I don't want to introduce a redundant overload just to suppress a bogus compiler warning.  How about a patch that just adds one of those MSVC #pragmas to disable the warning?

---

## Comment 2

> Username: fmhess  
> Created_at: 2014-08-16 00:30:02 UTC  
> Url: https://github.com/boostorg/signals2/pull/5#issuecomment-52376427  

Hmm, actually according to another ticket, newer versions of gcc generate similar warnings.  Maybe I will add the redundant overload.

---

## Comment 3

> Username: sergiud  
> Created_at: 2014-08-16 09:12:40 UTC  
> Url: https://github.com/boostorg/signals2/pull/5#issuecomment-52388260  

Thanks. As you fixed both problems, I guess the pull request can be closed now.

---
