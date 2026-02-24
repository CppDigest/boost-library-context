# #91 Implement min_element and max_element and sever algorithm dependency [Merged]

> Username: glenfe  
> Created at: 2019-04-22 22:53:38 UTC  
> Updated at: 2019-04-29 14:07:58 UTC  
> Merged at: 2019-04-22 23:13:54 UTC  
> Closed at: 2019-04-22 23:13:54 UTC  
> Url: https://github.com/boostorg/range/pull/91  



---

## Comment 1

> Username: morinmorin  
> Created_at: 2019-04-29 14:07:58 UTC  
> Url: https://github.com/boostorg/range/pull/91#issuecomment-487594132  

Thanks for fixing the issue!  
  
I'm not sure it's worth doing, but I think `less` can be `BOOST_CONSTEXPR` and `min_element`/`max_element` can be `BOOST_CXX14_CONSTEXPR`.

---
