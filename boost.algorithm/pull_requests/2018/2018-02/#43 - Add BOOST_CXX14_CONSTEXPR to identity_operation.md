# #43 Add BOOST_CXX14_CONSTEXPR to identity_operation. [Merged]

> Username: jeremy-murphy  
> Created at: 2018-02-05 13:52:15 UTC  
> Updated at: 2018-02-05 15:10:00 UTC  
> Merged at: 2018-02-05 15:09:53 UTC  
> Closed at: 2018-02-05 15:09:53 UTC  
> Url: https://github.com/boostorg/algorithm/pull/43  

`power(x, n, op)` uses `identity_operation` and is constexpr in C++14.  
  
On a tangential note... shouldn't these functions be called `identity_element`?

---

## Comment 1

> Username: mclow  
> Created_at: 2018-02-05 15:10:00 UTC  
> Url: https://github.com/boostorg/algorithm/pull/43#issuecomment-363112759  

Thanks!

---
