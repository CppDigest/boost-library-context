# #65 Fix for issue 48 [Merged]

> Username: mborland  
> Created at: 2022-07-10 16:37:59 UTC  
> Updated at: 2022-07-11 20:37:00 UTC  
> Merged at: 2022-07-11 20:37:00 UTC  
> Closed at: 2022-07-11 20:37:00 UTC  
> Url: https://github.com/boostorg/chrono/pull/65  

Fixes for -Wzero-as-null-pointer-constant by adding macro discussed on the ML for `nullptr`.

---

## Comment 1

> Username: mborland  
> Created_at: 2022-07-11 14:27:24 UTC  
> Url: https://github.com/boostorg/chrono/pull/65#issuecomment-1180480598  

@pdimov or @jzmaddock This and #64 are clean for review and merge.

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-07-11 14:58:13 UTC  
> Url: https://github.com/boostorg/chrono/pull/65#issuecomment-1180518700  

Why not use `BOOST_NULLPTR` directly?

---

## Comment 3

> Username: mborland  
> Created_at: 2022-07-11 17:38:21 UTC  
> Url: https://github.com/boostorg/chrono/pull/65#issuecomment-1180685418  

> Why not use `BOOST_NULLPTR` directly?  
  
I didn't realize it had been merged. Change has been made.

---
