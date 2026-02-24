# #14 Workaround for bogus [-Wignored-attributes] warning on GCC 6.x/7.x [Merged]

> Username: pgroke-dt  
> Created at: 2017-10-03 11:42:49 UTC  
> Updated at: 2017-10-09 08:04:20 UTC  
> Merged at: 2017-10-07 22:45:12 UTC  
> Closed at: 2017-10-07 22:45:12 UTC  
> Url: https://github.com/boostorg/move/pull/14  

I've executed the Boost.Move and Boost.Container tests with GCC 6.3 & MSVC 2017 => pass.  
  
I've also verified the general "technique" (wrapping the struct with the alignment attribute in another struct) with several GCC, ICC and Clang versions, and it seems to work fine - i.e. no bugs where the compiler "loses" the alignment for the wrapper struct or the wrapper struct comes out with an unexpected size.  
  
So I guess this should be safe.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2017-10-07 22:45:44 UTC  
> Url: https://github.com/boostorg/move/pull/14#issuecomment-334970722  

Retested locally for several GCC and MSVC versions, looks fine. Many thanks for the patch!

---

## Comment 2

> Username: pgroke-dt  
> Created_at: 2017-10-09 08:03:54 UTC  
> Url: https://github.com/boostorg/move/pull/14#issuecomment-335088877  

You're welcome! Thanks for re-testing and merging :)

---
