# #77 Changes for Embarcadero C++ clang-based compilers, targeting Boost 1.74 [Merged]

> Username: eldiener  
> Created at: 2020-04-19 07:02:57 UTC  
> Updated at: 2020-05-18 22:57:38 UTC  
> Merged at: 2020-05-18 22:57:38 UTC  
> Closed at: 2020-05-18 22:57:38 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/77  



---

## Comment 1

> Username: eldiener  
> Created_at: 2020-05-06 12:25:54 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/77#issuecomment-624618490  

Can this please be merged ?

---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2020-05-06 12:52:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/smart_ptr/pull/77#pullrequestreview-406580278  

📁 include/boost/smart_ptr/detail/shared_count.hpp

```diff
 663 | 
 664 |- #ifdef __BORLANDC__
 664 |+ #ifdef BOOST_BORLANDC
```

> Username: pdimov  
> Created_at: 2020-05-06 12:52:40 UTC  
> Updated_at: 2020-05-06 15:32:34 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/77#discussion_r420765346  

This change doesn't agree with the previous one; this uses BOOST_BORLANDC, the other does not.

> Username: eldiener  
> Created_at: 2020-05-06 12:57:39 UTC  
> Updated_at: 2020-05-06 15:32:34 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/77#discussion_r420768653  

If config.hpp is not included or not yet included we can not use BOOST_BORLANDC but instead must use the long form of __BORLANDC__ and !__clang__.

> Username: pdimov  
> Created_at: 2020-05-06 13:13:56 UTC  
> Updated_at: 2020-05-06 15:32:34 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/77#discussion_r420779358  

If you use one condition for the `# pragma warn -8027`, you must use the same condition for the subsequent `# pragma warn .8027`.

> Username: eldiener  
> Created_at: 2020-05-06 15:33:03 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/77#discussion_r420886862  

Done !


---

## Comment 3

> Username: eldiener  
> Created_at: 2020-05-18 19:29:50 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/77#issuecomment-630391148  

Is there any reason this can not be merged ? There are no conflicts and all tests pass.

---
