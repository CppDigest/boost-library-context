# #16 Fixes empty example [Merged]

> Username: teeks99  
> Created at: 2015-04-11 00:49:42 UTC  
> Updated at: 2015-06-24 22:01:25 UTC  
> Merged at: 2015-06-24 22:01:24 UTC  
> Closed at: 2015-06-24 22:01:24 UTC  
> Url: https://github.com/boostorg/python/pull/16  

Uses of visual studio were getting a 'unresolved external symbol _mainCRTStartup' because there was no main() being linked into the test_embed.exe. I'm not a bjam expert, but I think that is because the embedding target is an executable (instead of an obj/lib?). I also have no idea if this was a problem on unix or if this fix will affect that platform.

---

## Comment 1

> Username: teeks99  
> Created_at: 2015-05-13 13:20:19 UTC  
> Url: https://github.com/boostorg/python/pull/16#issuecomment-101660740  

I've now been able to run this on linux as well, and can verify that it doesn't cause any problems with the examples.

---

## Comment 2

> Username: teeks99  
> Created_at: 2015-06-24 16:44:10 UTC  
> Url: https://github.com/boostorg/python/pull/16#issuecomment-114938269  

Any chance of getting this in before the freeze for 1.59 coming up next week?

---
