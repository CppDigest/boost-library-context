# #159 Remove dependency on boost/algorithm/clamp.hpp [Merged]

> Username: mloskot  
> Created at: 2018-10-22 21:34:39 UTC  
> Updated at: 2018-10-29 12:49:09 UTC  
> Merged at: 2018-10-29 12:48:15 UTC  
> Closed at: 2018-10-29 12:48:15 UTC  
> Url: https://github.com/boostorg/gil/pull/159  

Copy `boost::algorithm::clamp` to where it was solely used in Toolbox.  
Add `subchroma_image.cpp` missing from build target of Toolbox test.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2018-10-22 21:40:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/159#pullrequestreview-167121772  

📁 include/boost/gil/extension/toolbox/color_spaces/ycbcr.hpp

```diff
  61 |+ // Source:boost/algorithm/clamp.hpp
  62 |+ template<typename T>
  63 |+ BOOST_CXX14_CONSTEXPR T const& clamp(T const& val,
```

> Username: stefanseefeld  
> Created_at: 2018-10-22 21:40:03 UTC  
> Updated_at: 2018-10-28 21:55:03 UTC  
> Url: https://github.com/boostorg/gil/pull/159#discussion_r227148189  

Does this have to be constexpr ? Or, to put it differently, is it worth introducing a C++14-only requirement here ?

> Username: mloskot  
> Created_at: 2018-10-22 22:04:02 UTC  
> Updated_at: 2018-10-28 21:55:03 UTC  
> Url: https://github.com/boostorg/gil/pull/159#discussion_r227154650  

Good question. Perhaps C++11 `constexpr` will be sufficient.   
  
Unfortunately, toolbox tests are currently failing to compile with GCC for me, so I'll have to look at those issues first.

> Username: mloskot  
> Created_at: 2018-10-28 21:55:58 UTC  
> Url: https://github.com/boostorg/gil/pull/159#discussion_r228767255  

I've replaced `BOOST_CXX14_CONSTEXPR` with `constexpr`.

> Username: stefanseefeld  
> Created_at: 2018-10-28 22:58:48 UTC  
> Url: https://github.com/boostorg/gil/pull/159#discussion_r228769783  

Excellent, thanks !


---
