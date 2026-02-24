# #319 Add basic test for color base algorithm: static_transform [Merged]

> Username: mloskot  
> Created at: 2019-07-02 19:31:11 UTC  
> Updated at: 2019-07-03 13:29:44 UTC  
> Merged at: 2019-07-03 05:40:48 UTC  
> Closed at: 2019-07-03 05:40:48 UTC  
> Url: https://github.com/boostorg/gil/pull/319  

The test also illustrates and verifies that the `static_transform` is constrained by the size of destination color base.  
That is, it is applicable if the source color base has number of elements equal-to or greater-than the destination color base.  
  
------  
  
I am not entirely sure if this behaviour is by design or by some sort of accident. For example, it is possible that some compile-time checks are simply missing e.g. refusing the case source of wider to narrower color base and requiring equal size of both color bases.  
However, this test verifies (and documents) current behaviour w/o changing it.  
  
### Tasklist  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-07-02 22:14:10 UTC  
> Url: https://github.com/boostorg/gil/pull/319#issuecomment-507864480  

My private Travis CI passed: https://travis-ci.org/mloskot/gil/builds/553438326

---

## Review 2 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-07-02 22:20:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/319#pullrequestreview-257178979  

📁 test/core/color_base/static_transform_gray_to_rgb_fail.cpp

```diff
  20 |+     // of the *destination* color base.
  21 |+ 
  22 |+     // RGB (wider space) transformation to Gray (narrower space) takes only R channel value
```

> Username: stefanseefeld  
> Created_at: 2019-07-02 22:20:53 UTC  
> Url: https://github.com/boostorg/gil/pull/319#discussion_r299708362  

It isn't immediately obvious to me why this operation should fail. Why doesn't the `static_transform` operation allow arbitrary mappings from source to destination color spaces ?

> Username: mloskot  
> Created_at: 2019-07-02 23:10:35 UTC  
> Updated_at: 2019-07-02 23:11:36 UTC  
> Url: https://github.com/boostorg/gil/pull/319#discussion_r299720091  

It's how it is implemented as static ir compile-time unrolled iteration through pixel elements (channels) which is also displayed by this assertion(s)   
  
https://github.com/boostorg/gil/blob/c9ccc8355108b53e06cd62ef88d80cf802bfb5b8/include/boost/gil/color_base_algorithm.hpp#L93-L94


---

## Review 3 [Approved]

> Username: stefanseefeld  
> Created_at: 2019-07-02 22:21:21 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/319#pullrequestreview-257179141  

Thanks for improving the test coverage !

---
