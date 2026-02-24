# #125 Update version numbers to reflect VC++ 2017 RTW, aka RTM [Closed]

> Username: Beman  
> Created at: 2017-03-11 21:26:58 UTC  
> Updated at: 2017-03-13 19:00:51 UTC  
> Closed at: 2017-03-13 19:00:51 UTC  
> Url: https://github.com/boostorg/config/pull/125  

VC++ 2017 shipped March 7th. This P/R reflects the actual RTW version numbers. Note: Release to Manufacturing (RTM) is now called Release to Web (RTW).  
  
Also note that Microsoft has also changed to incrementing  _MSC_VER monotonically for each release.  
  
Please review carefully. I tested only with the Visual Studio IDE since Boost.Build does not work yet for VC++2017.  
  
--Beman

---

## Review 1 [Changes requested]

> Username: jhunold  
> Created_at: 2017-03-11 23:27:24 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/config/pull/125#pullrequestreview-26433401  

See https://github.com/boostorg/build/issues/157 and https://github.com/boostorg/build/pull/167 for the relevant discussion which led to use 14.10 instead of 15.0

📁 include/boost/config/compiler/visualc.hpp

```diff
 307 | #     define BOOST_COMPILER_VERSION 14.0
 308 |+ #   elif _MSC_VER < 1911
 309 |+ #     define BOOST_COMPILER_VERSION 15.0
```

> Username: jhunold  
> Created_at: 2017-03-11 23:24:31 UTC  
> Updated_at: 2017-03-11 23:27:24 UTC  
> Url: https://github.com/boostorg/config/pull/125#discussion_r105544780  

This should be 14.10 in order to be consistend with the latest Boost.Build patches.  
`#     define BOOST_COMPILER_VERSION 14.10`


---

## Comment 2

> Username: KindDragon  
> Created_at: 2017-03-13 13:39:17 UTC  
> Url: https://github.com/boostorg/config/pull/125#issuecomment-286109155  

PR #119 should fix this

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2017-03-13 19:00:47 UTC  
> Url: https://github.com/boostorg/config/pull/125#issuecomment-286209475  

This is now addressed in develop.

---
