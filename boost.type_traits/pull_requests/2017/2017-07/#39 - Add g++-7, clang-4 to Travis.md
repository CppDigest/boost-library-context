# #39 Add g++-7, clang-4 to Travis [Merged]

> Username: pdimov  
> Created at: 2017-07-15 11:38:31 UTC  
> Updated at: 2017-07-15 15:25:41 UTC  
> Merged at: 2017-07-15 15:09:29 UTC  
> Closed at: 2017-07-15 15:09:29 UTC  
> Url: https://github.com/boostorg/type_traits/pull/39  

Adding PR to kick off the CI; when it finishes, I will update with fixes.

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-07-15 14:20:45 UTC  
> Url: https://github.com/boostorg/type_traits/pull/39#issuecomment-315536941  

Everything is green now.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2017-07-15 15:10:10 UTC  
> Url: https://github.com/boostorg/type_traits/pull/39#issuecomment-315540529  

Note to self: should really fix the warnings rather than disable them... seems like every new compiler release has some new ones...

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-07-15 15:25:41 UTC  
> Url: https://github.com/boostorg/type_traits/pull/39#issuecomment-315541538  

The warnings here are unavoidable, because the tests test, f.ex. whether `operator*` for `bool` exists, and it exists, but warns.  
  
The implementation doesn't emit these warnings during normal use.  
  
Of course disabling the warnings in the Jamfile may hide their being emitted during normal use, so there is that.

---
