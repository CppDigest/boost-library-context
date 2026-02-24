# #194 - Insufficient State Limit for Resolving Regex [Closed]

> Username: MarcoDeMeco  
> Created at: 2023-06-12 10:18:23 UTC  
> Updated at: 2024-03-25 18:16:19 UTC  
> Closed at: 2024-03-25 18:16:19 UTC  
> Url: https://github.com/boostorg/regex/issues/194  

Boost 1.74  
Using the following regex  
^\s*((\s?\S+)+)\s*$  
with the input "Intel Corporation", the library throws an error because the corresponding heuristic has generated a lower state limit than the number of states required to solve the regex. I'm wondering if it's possible to adjust this heuristic to provide a slightly higher number of states so that this regex can be resolved by the library.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-03-25 18:16:19 UTC  
> Url: https://github.com/boostorg/regex/issues/194#issuecomment-2018621669  

This looks to be working OK in current releases.  Please reopen if it re-surfaces.
