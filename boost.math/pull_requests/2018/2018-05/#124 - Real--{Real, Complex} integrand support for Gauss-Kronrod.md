# #124 Real->{Real, Complex} integrand support for Gauss-Kronrod [Merged]

> Username: NAThompson  
> Created at: 2018-05-01 07:52:14 UTC  
> Updated at: 2018-05-05 01:52:00 UTC  
> Merged at: 2018-05-04 18:18:51 UTC  
> Closed at: 2018-05-04 18:18:52 UTC  
> Url: https://github.com/boostorg/math/pull/124  

Couldn't get it to work with `cpp_dec_float`, but everything else works.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-05-01 19:17:26 UTC  
> Url: https://github.com/boostorg/math/pull/124#issuecomment-385762372  

This fails with expression templates enabled when the function being integrated returns an expression template, for example in our tests we have:  
  
```    auto f = [](const Real& x)  
    {  
       return 5*x + 7;  
    };  
```  
  
I guess it would be nice to retain this, will look at it later.

---

## Comment 2

> Username: NAThompson  
> Created_at: 2018-05-02 01:32:17 UTC  
> Url: https://github.com/boostorg/math/pull/124#issuecomment-385838873  

I'm not sure it's a big priority. Plus the tests are now consistently using the `auto [](Real t)->Real` syntax.

---
