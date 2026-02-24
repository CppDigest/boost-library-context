# #44 Fix warnings in tests [Merged]

> Username: Lastique  
> Created at: 2017-08-10 16:13:11 UTC  
> Updated at: 2017-08-11 15:07:54 UTC  
> Merged at: 2017-08-11 13:25:47 UTC  
> Closed at: 2017-08-11 13:25:47 UTC  
> Url: https://github.com/boostorg/date_time/pull/44  

- Fixes "invalid character" warnings by clang.  
- Fixes "signed/unsigned mismatch" in comparisons by gcc.  
- Fixes "unused variable" warning from clang.  
  
All warnings are specific for tests; no library code affected.

---

## Review 1 [Commented]

> Username: eldiener  
> Created_at: 2017-08-10 16:31:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/44#pullrequestreview-55592260  

📁 test/testfrmwk.hpp

```diff
  69 |+ # pragma GCC diagnostic push
  70 |+ # pragma GCC diagnostic ignored "-Wsign-compare"
  71 |+ #endif
```

> Username: eldiener  
> Created_at: 2017-08-10 16:31:51 UTC  
> Updated_at: 2017-08-11 13:10:11 UTC  
> Url: https://github.com/boostorg/date_time/pull/44#discussion_r132504052  

Can't we just include config.hpp and then use BOOST_MSVC, BOOST_CLANG, and BOOST_GCC instead of _MSC_VER, __clang__, and the whole gcc low-level check you have in here ? Why should we reproduce what is already in Boost config and make the code more unreadable at the same time ?

> Username: Lastique  
> Created_at: 2017-08-10 16:51:34 UTC  
> Updated_at: 2017-08-11 13:10:11 UTC  
> Url: https://github.com/boostorg/date_time/pull/44#discussion_r132508624  

The intention here is to only exclude Intel compiler. As for `_MSC_VER` - it will disable the warning for any MSVC-compatible compiler, not just genuine MSVC.

> Username: eldiener  
> Created_at: 2017-08-11 00:02:27 UTC  
> Updated_at: 2017-08-11 13:10:11 UTC  
> Url: https://github.com/boostorg/date_time/pull/44#discussion_r132597562  

Would it not be safer just deal with vc++, clang, and gcc itself rather than assume that other compilers which may be compatible to any of them use the same pragmas to disable the warning ? You seem sure that compatible compilers,  except for the Intel case, use the same pragmas.

> Username: Lastique  
> Created_at: 2017-08-11 12:54:45 UTC  
> Updated_at: 2017-08-11 13:10:11 UTC  
> Url: https://github.com/boostorg/date_time/pull/44#discussion_r132678565  

Well, I kind of expect that compatible compilers are... compatible. I took this code from Boost.Core, where it's been used for some time without problems. But anyway, it's not a big deal, I can limit the pragmas to those specific compilers.

> Username: Lastique  
> Created_at: 2017-08-11 13:11:04 UTC  
> Url: https://github.com/boostorg/date_time/pull/44#discussion_r132681575  

Done. Also silenced another warning from clang about unused variable.


---
