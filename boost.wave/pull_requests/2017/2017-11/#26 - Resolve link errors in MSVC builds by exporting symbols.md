# #26 Resolve link errors in MSVC builds by exporting symbols [Merged]

> Username: jefftrull  
> Created at: 2017-11-30 03:30:10 UTC  
> Updated at: 2017-12-01 02:50:17 UTC  
> Merged at: 2017-12-01 02:50:17 UTC  
> Closed at: 2017-12-01 02:50:17 UTC  
> Url: https://github.com/boostorg/wave/pull/26  

In the forward iterator PR a number of functions became function templates and so moved into .hpp files. As a result some symbols were not locally visible to their users anymore, and now need to be exported for MSVC builds.

---

## Review 1 [Commented]

> Username: hkaiser  
> Created_at: 2017-11-30 13:40:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/wave/pull/26#pullrequestreview-80183541  

📁 include/boost/wave/cpplexer/re2clex/cpp_re.hpp

```diff
 365 | 
 366 |     uchar *base_cursor;
 367 |     std::size_t column;
```

> Username: hkaiser  
> Created_at: 2017-11-30 13:40:20 UTC  
> Updated_at: 2017-11-30 16:36:11 UTC  
> Url: https://github.com/boostorg/wave/pull/26#discussion_r154076489  

It might be better to add the `BOOST_WAVE_DECL` to the whole class (`struct BOOST_WAVE_DECL uchar_wrapper {...};`) then to every single member functions.

> Username: jefftrull  
> Created_at: 2017-11-30 15:53:43 UTC  
> Updated_at: 2017-11-30 16:36:11 UTC  
> Url: https://github.com/boostorg/wave/pull/26#discussion_r154116201  

Ah.... you can do that? Fixing :)


---

## Review 2 [Approved]

> Username: hkaiser  
> Created_at: 2017-11-30 20:20:28 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/26#pullrequestreview-80316837  

LGTM, thanks!

---
