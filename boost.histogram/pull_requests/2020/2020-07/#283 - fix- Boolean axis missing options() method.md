# #283 fix: Boolean axis missing options() method [Merged]

> Username: henryiii  
> Created at: 2020-07-03 15:17:23 UTC  
> Updated at: 2020-07-05 18:19:22 UTC  
> Merged at: 2020-07-05 06:45:18 UTC  
> Closed at: 2020-07-05 06:45:18 UTC  
> Url: https://github.com/boostorg/histogram/pull/283  

The new boolean axis is missing an `options()` method, which causes it to break in code that expects all axes to have an options method (boost-histogram, for example). This adds this method (hopefully in time for 1.74).

---

## Comment 1

> Username: HDembinski  
> Created_at: 2020-07-05 06:45:05 UTC  
> Url: https://github.com/boostorg/histogram/pull/283#issuecomment-653849472  

Thank you, I will merge this, but your code should not rely on options(), it is an optional axis feature, see https://www.boost.org/doc/libs/develop/libs/histogram/doc/html/histogram/concepts.html  
  
Please use axis::traits::options() for uniform access.

---

## Comment 2

> Username: henryiii  
> Created_at: 2020-07-05 18:19:22 UTC  
> Url: https://github.com/boostorg/histogram/pull/283#issuecomment-653921511  

Thank you, I think this is still more consistent; Boost.Histogram should provide the optional features if possible -  It's great that it's not required, as well, though.   
  
Actually, for my usage, `axis::traits::get_options` seems to be perfect, adapted the boost-histogram code to consistently use that. :)

---
