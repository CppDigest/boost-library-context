# #62 Use BOOST_VERIFY instead of conditional BOOST_ASSERT [Merged]

> Username: karcherm  
> Created at: 2024-01-12 13:12:37 UTC  
> Updated at: 2024-08-28 10:50:46 UTC  
> Merged at: 2024-08-28 10:50:39 UTC  
> Closed at: 2024-08-28 10:50:39 UTC  
> Url: https://github.com/boostorg/coroutine/pull/62  

This change  
- reduces complexity of the code in Boost.Coroutine due to the elimination of preprocessor conditionals  
- removes code duplication  
- prevents a warning (on some compilers) in case `BOOST_DISABLE_ASSERTS` is not defined, but `NDEBUG` is defined

---

## Comment 1

> Username: olk  
> Created_at: 2024-08-28 10:50:44 UTC  
> Url: https://github.com/boostorg/coroutine/pull/62#issuecomment-2314979750  

ty

---
