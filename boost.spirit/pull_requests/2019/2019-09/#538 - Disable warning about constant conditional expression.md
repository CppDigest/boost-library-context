# #538 Disable warning about constant conditional expression. [Closed]

> Username: rubenvb  
> Created at: 2019-09-21 12:40:56 UTC  
> Updated at: 2019-09-30 11:09:49 UTC  
> Closed at: 2019-09-30 11:09:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/538  

Fixes issue #537, part 1.

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2019-09-21 13:47:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/538#pullrequestreview-291465936  

📁 include/boost/spirit/home/x3/char/detail/cast_char.hpp

```diff
  24 |     // optimizer will optimize the if-else branches}
  25 |- 
  25 |+ #if BOOST_WORKAROUND(BOOST_MSVC, >= 1400)
```

> Username: Kojoley  
> Created_at: 2019-09-21 13:47:12 UTC  
> Updated_at: 2019-09-22 08:55:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/538#discussion_r326860585  

Please, either include boost workaround header or do not use it. Also, boost is not tested/supported under msvc < 16 for a while already, so you can just check for msvc compiler, i.e. just `ifdef _MSC_VER `.

> Username: rubenvb  
> Created_at: 2019-09-22 08:56:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/538#discussion_r326890438  

I included the relevant header. I kept BOOST_WORKAROUND because that's how this kind of thing is done in all other Spirit headers.


---

## Comment 2

> Username: Kojoley  
> Created_at: 2019-09-30 11:09:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/538#issuecomment-536514791  

Superseded by #541

---
