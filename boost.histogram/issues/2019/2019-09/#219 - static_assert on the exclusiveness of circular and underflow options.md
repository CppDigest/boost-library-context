# #219 - static_assert on the exclusiveness of circular and underflow options [Closed]

> Username: HDembinski  
> Created at: 2019-09-25 10:58:09 UTC  
> Updated at: 2019-10-01 10:21:55 UTC  
> Closed at: 2019-10-01 10:21:55 UTC  
> Url: https://github.com/boostorg/histogram/issues/219  

When options are OR'd together, use static_assert? This seems brittle. Probably better to do a static_assert in each axis that receives options.

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-10-01 10:21:55 UTC  
> Url: https://github.com/boostorg/histogram/issues/219#issuecomment-536972109  

The builtin axes check the options now.
