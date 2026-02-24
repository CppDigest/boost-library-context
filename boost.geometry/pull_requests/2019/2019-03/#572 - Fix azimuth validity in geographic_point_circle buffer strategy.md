# #572 Fix azimuth validity in geographic_point_circle buffer strategy. [Merged]

> Username: awulkiew  
> Created at: 2019-03-15 16:35:06 UTC  
> Updated at: 2019-06-26 13:25:38 UTC  
> Merged at: 2019-06-26 13:25:38 UTC  
> Closed at: 2019-06-26 13:25:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/572  

This PR also adds asserts in formulas.  
  
It addresses the issue: https://github.com/boostorg/geometry/issues/542  
  
It also fixed some failing cases for me from algorithms_buffer_point_geo test file (tested only with msvc-14.1).

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2019-03-18 14:22:55 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/572#pullrequestreview-215628069  

Thanks!

---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2019-04-03 07:59:01 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/572#pullrequestreview-222052637  

Note the potentially unused variable, but basically I'm OK

📁 include/boost/geometry/formulas/differential_quantities.hpp

```diff
  76 |             {
  77 |+                 CT const pi = math::pi<CT>();
  78 |+                 BOOST_GEOMETRY_ASSERT(-pi <= azimuth && azimuth <= pi);
```

> Username: barendgehrels  
> Created_at: 2019-04-03 07:56:08 UTC  
> Updated_at: 2019-04-03 13:51:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/572#discussion_r271618125  

You will get a warning for an unused variable pi in release mode. Maybe just use the math::pi<CT>() inside the assertion?


📁 include/boost/geometry/strategies/geographic/buffer_point_circle.hpp

```diff
  99 |         {
 100 |+             if (angle > pi)
 101 |+                 angle -= two_pi;
```

> Username: barendgehrels  
> Created_at: 2019-04-03 07:56:41 UTC  
> Updated_at: 2019-04-03 13:51:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/572#discussion_r271618293  

please use braces in every if - this is our convention

> Username: barendgehrels  
> Created_at: 2019-04-03 07:57:58 UTC  
> Updated_at: 2019-04-03 13:51:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/572#discussion_r271618729  

Thanks for fixing this!


---

## Comment 3

> Username: barendgehrels  
> Created_at: 2019-04-03 14:47:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/572#issuecomment-479521972  

@awulkiew : Thanks for the changes!

---
