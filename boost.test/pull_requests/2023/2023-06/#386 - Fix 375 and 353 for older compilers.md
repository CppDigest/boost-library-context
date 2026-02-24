# #386 Fix 375 and 353 for older compilers [Merged]

> Username: mborland  
> Created at: 2023-06-13 12:01:43 UTC  
> Updated at: 2023-06-16 16:45:16 UTC  
> Merged at: 2023-06-16 07:37:48 UTC  
> Closed at: 2023-06-16 07:37:48 UTC  
> Url: https://github.com/boostorg/test/pull/386  

Add GCC 4.6 to see if other issues with ancient compilers exist

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2023-06-14 12:17:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/test/pull/386#pullrequestreview-1479283582  

📁 include/boost/test/impl/execution_monitor.ipp

```diff
 195 | #endif
 196 | 
 197 |+ #if !defined(BOOST_MSVC) || (defined(BOOST_MSVC) && BOOST_MSVC >= 1800)
```

> Username: Lastique  
> Created_at: 2023-06-14 12:17:28 UTC  
> Updated_at: 2023-06-14 12:18:09 UTC  
> Url: https://github.com/boostorg/test/pull/386#discussion_r1229513691  

I think, this needs to check `_MSC_VER` or even `BOOST_MSSTL_VERSION` instead of `BOOST_MSVC`, since any compiler that uses MS standard library will be affected. That may be Intel compiler or clang, for example.  
  
Also, you don't need to check for `defined` twice.


---

## Review 2 [Commented]

> Username: Lastique  
> Created_at: 2023-06-15 08:25:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/test/pull/386#pullrequestreview-1480991718  

📁 include/boost/test/detail/config.hpp

```diff
 163 |+ // See: https://github.com/boostorg/test/issues/385
 164 |+ 
 165 |+ #if (defined(BOOST_MSVC) && BOOST_MSVC >= 1900) || (defined(__GNUC__) && !defined(__clang__) && !defined(BOOST_INTEL) && (__GNUC__ >= 5 || __GNUC_MINOR__ >= 7))
```

> Username: Lastique  
> Created_at: 2023-06-15 08:25:52 UTC  
> Updated_at: 2023-06-15 08:25:53 UTC  
> Url: https://github.com/boostorg/test/pull/386#discussion_r1230636024  

You can use `BOOST_GCC` here. It is only defined for genuine gcc.


---

## Comment 3

> Username: mborland  
> Created_at: 2023-06-16 07:37:29 UTC  
> Url: https://github.com/boostorg/test/pull/386#issuecomment-1594250432  

@Lastique I am going to merge this now that the CI cycled. Let me know if you have any remaining issues after the fixes.

---

## Comment 4

> Username: Lastique  
> Created_at: 2023-06-16 10:22:36 UTC  
> Url: https://github.com/boostorg/test/pull/386#issuecomment-1594459480  

> @Lastique I am going to merge this now that the CI cycled. Let me know if you have any remaining issues after the fixes.  
  
Ok, thanks. I'll rerun Boost.Log CI. It'll take time for it to complete.

---

## Comment 5

> Username: Lastique  
> Created_at: 2023-06-16 16:45:16 UTC  
> Url: https://github.com/boostorg/test/pull/386#issuecomment-1594968338  

The CI has passed without errors. Thanks again.

---
