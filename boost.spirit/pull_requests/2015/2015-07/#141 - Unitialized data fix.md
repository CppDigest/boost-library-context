# #141 Unitialized data fix [Merged]

> Username: vtnerd  
> Created at: 2015-07-14 19:48:03 UTC  
> Updated at: 2015-07-31 03:33:56 UTC  
> Merged at: 2015-07-14 22:34:06 UTC  
> Closed at: 2015-07-14 22:34:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/141  

This is a fix for the issue posted on the mailing list. I don't see any other unitialized data values. I don't think a test can be written unless `n` is initialized, otherwise the test will be unreliable (n _usually_ equals some small nearly zero value on my box, but it is undefined ....).

---

## Comment 1

> Username: vtnerd  
> Created_at: 2015-07-14 19:48:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/141#issuecomment-121356824  

Also note, this is issued against the developer branch, and not master ...

---

## Comment 2

> Username: djowel  
> Created_at: 2015-07-14 22:32:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/141#issuecomment-121412976  

I'll merge this one and merge to master asap.   
  
I understand now why the test might be unreliable. I guess there's no easy way to deal with this other than test against valgrind.

---
