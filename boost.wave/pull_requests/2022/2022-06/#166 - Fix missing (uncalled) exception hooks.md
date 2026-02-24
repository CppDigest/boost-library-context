# #166 Fix missing (uncalled) exception hooks [Merged]

> Username: jefftrull  
> Created at: 2022-06-22 06:10:25 UTC  
> Updated at: 2022-06-22 18:22:27 UTC  
> Merged at: 2022-06-22 18:22:27 UTC  
> Closed at: 2022-06-22 18:22:27 UTC  
> Url: https://github.com/boostorg/wave/pull/166  

This PR does two main things:  
  
1. Ensures we check the hook results in unit tests after an _expected_ error occurs, as well as when no error occurs. We were missing some test coverage.  
2. Fixes a bug the freshened tests reveal: that sometimes hooks are not called (which is also the subject of #161, which this PR resolves). Thanks to @abakhirkin for the fix.

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2022-06-22 12:58:03 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/166#pullrequestreview-1015124414  

LGTM, thanks!

📁 include/boost/wave/cpp_throw.hpp

```diff
 167 | #define BOOST_WAVE_THROW_NAME_CTX(ctx, cls, code, msg, act_pos, name)         \
 168 |-     boost::wave::util::throw_<cls>(cls::code, msg, act_pos, name)             \
 168 |+     boost::wave::util::throw_<cls>(ctx, cls::code, msg, act_pos, name)        \
```

> Username: hkaiser  
> Created_at: 2022-06-22 12:55:41 UTC  
> Updated_at: 2022-06-22 12:58:03 UTC  
> Url: https://github.com/boostorg/wave/pull/166#discussion_r903707584  

Excellent catch!

> Username: jefftrull  
> Created_at: 2022-06-22 18:20:32 UTC  
> Updated_at: 2022-06-22 18:20:33 UTC  
> Url: https://github.com/boostorg/wave/pull/166#discussion_r904098800  

We have @abakhirkin to thank for that!


---
