# #59 generate indexed_image_view [Merged]

> Username: chhenning  
> Created at: 2018-03-22 14:30:33 UTC  
> Updated at: 2018-10-23 17:03:10 UTC  
> Merged at: 2018-03-23 15:03:52 UTC  
> Closed at: 2018-03-23 15:03:52 UTC  
> Url: https://github.com/boostorg/gil/pull/59  

### Description  
  
Added new function to generate an indexed_image_view from two views. One for indices and palette.  
  
### Tasklist  
  
- [x] Add test case(s)  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
### Environment  
  
All relevant information like:  
  
- Boost version 1.67  
- Compiler version: Visual Studio 2017

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2018-03-23 13:53:35 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/59#pullrequestreview-106279276  

Two comments on need to fix compilation with GCC/clang and code clarification.

📁 include/boost/gil/extension/toolbox/image_types/indexed_image.hpp

```diff
 237 |+         iv.dimensions()
 238 |+         , pv.dimensions().x
 239 |+         , view_t::locator(point_t(0, 0), point_t(1, 1), defer_fn_t(iv.xy_at(0, 0), pv.xy_at(0, 0)))
```

> Username: mloskot  
> Created_at: 2018-03-22 19:58:30 UTC  
> Updated_at: 2018-03-23 14:00:48 UTC  
> Url: https://github.com/boostorg/gil/pull/59#discussion_r176553953  

https://travis-ci.org/boostorg/gil/jobs/356904919#L2524  
```  
./boost/gil/extension/toolbox/image_types/indexed_image.hpp:239:26: error: dependent-name ‘view_t:: locator’ is parsed as a non-type, but instantiation yields a type  
         , view_t::locator(point_t(0, 0), point_t(1, 1), defer_fn_t(iv.xy_at(0, 0), pv.xy_at(0, 0)))  
                          ^  
./boost/gil/extension/toolbox/image_types/indexed_image.hpp:239:26: note: say ‘typename view_t:: locator’ if a type is meant  
```

> Username: chhenning  
> Created_at: 2018-03-23 13:55:11 UTC  
> Updated_at: 2018-03-23 14:00:48 UTC  
> Url: https://github.com/boostorg/gil/pull/59#discussion_r176741092  

I assume a `typename` is missing?

> Username: mloskot  
> Created_at: 2018-03-23 13:57:45 UTC  
> Updated_at: 2018-03-23 14:00:48 UTC  
> Url: https://github.com/boostorg/gil/pull/59#discussion_r176742097  

Yes, I think so


📁 toolbox/test/indexed_image_test.cpp

```diff
 161 |+ 
 162 |+     // indices
 163 |+     vector<uint8_t> indices(width * height, 2);
```

> Username: mloskot  
> Created_at: 2018-03-23 11:18:11 UTC  
> Updated_at: 2018-03-23 14:00:48 UTC  
> Url: https://github.com/boostorg/gil/pull/59#discussion_r176705125  

Where does this `2` magic number come from?

> Username: chhenning  
> Created_at: 2018-03-23 13:55:55 UTC  
> Updated_at: 2018-03-23 14:00:48 UTC  
> Url: https://github.com/boostorg/gil/pull/59#discussion_r176741383  

It's an index. The whole image just refers to the 3rd color in the palette.

> Username: mloskot  
> Created_at: 2018-03-23 13:57:35 UTC  
> Updated_at: 2018-03-23 14:00:48 UTC  
> Url: https://github.com/boostorg/gil/pull/59#discussion_r176742052  

It's minor issue, but since tests are also examples on how to use GIL, it's good to keep things more explicit (named constant, comment).


---

## Comment 2

> Username: mloskot  
> Created_at: 2018-03-23 14:39:11 UTC  
> Updated_at: 2018-03-23 14:39:21 UTC  
> Url: https://github.com/boostorg/gil/pull/59#issuecomment-375686184  

LGTM. I'm already using it :) Thanks!

---
