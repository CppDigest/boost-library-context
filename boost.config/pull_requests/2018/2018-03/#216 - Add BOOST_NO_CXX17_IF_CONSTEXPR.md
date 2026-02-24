# #216 Add BOOST_NO_CXX17_IF_CONSTEXPR. [Merged]

> Username: tzlaine  
> Created at: 2018-03-07 00:34:49 UTC  
> Updated at: 2018-04-18 18:26:48 UTC  
> Merged at: 2018-04-18 18:26:48 UTC  
> Closed at: 2018-04-18 18:26:48 UTC  
> Url: https://github.com/boostorg/config/pull/216  



---

## Comment 1

> Username: tzlaine  
> Created_at: 2018-03-07 00:35:51 UTC  
> Url: https://github.com/boostorg/config/pull/216#issuecomment-370980150  

Third time's the charm.  This passes all tests now.  Needed by Yap before it can be integrated as a Boost module.

---

## Comment 2

> Username: Lastique  
> Created_at: 2018-03-16 09:53:43 UTC  
> Url: https://github.com/boostorg/config/pull/216#issuecomment-373660748  

It might be useful to verify in the test the `if constexpr` statement actually discards one of the branches. For example, add a failing static assert in a dependent context.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2018-03-16 18:18:20 UTC  
> Url: https://github.com/boostorg/config/pull/216#issuecomment-373801609  

Other than Lastique's useful suggestion, this all looks good to me.  However, I won't merge until after the release.

---

## Comment 4

> Username: tzlaine  
> Created_at: 2018-03-17 19:10:24 UTC  
> Url: https://github.com/boostorg/config/pull/216#issuecomment-373945223  

Good idea.  I've added a static_assert to one of the false paths.

---
