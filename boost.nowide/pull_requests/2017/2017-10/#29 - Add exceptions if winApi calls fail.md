# #29 Add exceptions if winApi calls fail [Merged]

> Username: Flamefire  
> Created at: 2017-10-03 22:27:33 UTC  
> Updated at: 2019-12-14 19:52:19 UTC  
> Merged at: 2019-12-14 19:52:16 UTC  
> Closed at: 2019-12-14 19:52:16 UTC  
> Url: https://github.com/boostorg/nowide/pull/29  

Using `args` will no longer silently fail  
  
Fixes #15

---

## Comment 1

> Username: pdimov  
> Created_at: 2019-12-13 18:52:09 UTC  
> Url: https://github.com/boostorg/nowide/pull/29#issuecomment-565563115  

Doesn't the removal of the try-catch block cause a leak (of `wargv` and `wstrings`) on exception?

---

## Comment 2

> Username: Flamefire  
> Created_at: 2019-12-13 18:54:30 UTC  
> Updated_at: 2019-12-13 19:05:39 UTC  
> Url: https://github.com/boostorg/nowide/pull/29#issuecomment-565564009  

On which exception? IMO the only possible exception is a `std::bad_alloc` on `vector::resize`. Shall we catch that?

---

## Comment 3

> Username: pdimov  
> Created_at: 2019-12-13 18:57:05 UTC  
> Url: https://github.com/boostorg/nowide/pull/29#issuecomment-565565034  

That's exactly what the try-catch blocks did, freed `wargv` and `wstrings` on `bad_alloc`, then rethrew the exception. Removing them causes a leak. I don't understand what "shall we catch that" means.

---

## Comment 4

> Username: Flamefire  
> Created_at: 2019-12-13 19:18:15 UTC  
> Url: https://github.com/boostorg/nowide/pull/29#issuecomment-565573015  

Previously the `convert` threw on invalid UTF-8 which was (IMO) the intention for the try-catch.  
  
The question was because nobody really handles bad_alloc, but well. Better safe than sorry.

---

## Comment 5

> Username: Flamefire  
> Created_at: 2019-12-13 19:33:15 UTC  
> Url: https://github.com/boostorg/nowide/pull/29#issuecomment-565578389  

With hindsight to https://github.com/boostorg/nowide/issues/19 I added simple RAII classes instead. This should be cleaner too.

---
