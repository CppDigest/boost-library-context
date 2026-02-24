# #1232 - General RFC utilities, specifically char operations [Closed]

> Username: vinniefalco  
> Created at: 2018-08-23 03:07:34 UTC  
> Updated at: 2022-06-16 16:25:43 UTC  
> Closed at: 2022-06-16 16:25:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1232  

Things like `is_digit`, `ascii_tolower`, and the various URL char category functions (e.g. `is_pchar`) should probably be all rolled up into a single header file maybe in beast/core, along with its own set of tests, with an eye towards making these well documented and public.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2018-11-27 01:09:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1232#issuecomment-441859433  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-06-16 16:25:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1232#issuecomment-1157875750  

We aren't doing this anymore
