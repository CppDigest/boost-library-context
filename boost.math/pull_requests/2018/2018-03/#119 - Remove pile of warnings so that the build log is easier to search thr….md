# #119 Remove pile of warnings so that the build log is easier to search thr… [Merged]

> Username: NAThompson  
> Created at: 2018-03-17 08:40:18 UTC  
> Updated at: 2018-05-05 01:52:34 UTC  
> Merged at: 2018-05-04 18:28:50 UTC  
> Closed at: 2018-05-04 18:28:50 UTC  
> Url: https://github.com/boostorg/math/pull/119  

…ough.

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2018-03-18 19:13:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/119#pullrequestreview-104807967  

📁 test/compile_test/test_compile_result.hpp

```diff
  53 | {
  54 |    // This is a static assertion that should always fail to compile...
  55 |+ #pragma GCC diagnostic push
```

> Username: jzmaddock  
> Created_at: 2018-03-18 19:13:58 UTC  
> Url: https://github.com/boostorg/math/pull/119#discussion_r175300213  

Do we know which GCC version first started supporting this?

> Username: NAThompson  
> Created_at: 2018-03-19 02:08:12 UTC  
> Url: https://github.com/boostorg/math/pull/119#discussion_r175317546  

4.8: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=33255


---
