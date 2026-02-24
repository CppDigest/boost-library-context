# #5 Fix some unused parameter warnings. [Merged]

> Username: jeremy-murphy  
> Created at: 2015-12-15 10:12:40 UTC  
> Updated at: 2015-12-15 11:03:41 UTC  
> Merged at: 2015-12-15 10:16:33 UTC  
> Closed at: 2015-12-15 10:16:33 UTC  
> Url: https://github.com/boostorg/atomic/pull/5  

Hi! Sorry, I know very little about your library but I assume these are just unused parameters that have missed your attention. I ran the tests and they all passed. Cheers.

---

## Comment 1

> Username: timblechmann  
> Created_at: 2015-12-15 10:16:28 UTC  
> Url: https://github.com/boostorg/atomic/pull/5#issuecomment-164713242  

looks good, thanks a lot!

---

## Comment 2

> Username: Lastique  
> Created_at: 2015-12-15 10:28:30 UTC  
> Url: https://github.com/boostorg/atomic/pull/5#issuecomment-164717260  

I'd rather disable such warnings with a pragma in the future. Parameter names serve documentation purpose, even if not used.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2015-12-15 10:37:19 UTC  
> Url: https://github.com/boostorg/atomic/pull/5#issuecomment-164721236  

Ah, OK, what's the portable way to do that? With an actual `#pragma` or `BOOST_ATTRIBUTE_UNUSED`?

---

## Comment 4

> Username: Lastique  
> Created_at: 2015-12-15 11:03:41 UTC  
> Url: https://github.com/boostorg/atomic/pull/5#issuecomment-164729472  

I don't think `BOOST_ATTRIBUTE_UNUSED` has any effect on MSVC. So yes, I meant the actual `#pragma`, and since every compiler has its own set of warnings and ways to disable them, this would not be quite portable. Still, the effect can be achieved on most popular compilers. From my experience, it's best to introduce prefix and postfix headers that disable/restore offending warnings and include those headers at the beginning and the end of every other header of the library. I guess I'll have to introduce such headers for Boost.Atomic.

---
