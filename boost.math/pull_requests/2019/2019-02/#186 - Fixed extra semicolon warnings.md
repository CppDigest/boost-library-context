# #186 Fixed extra semicolon warnings [Closed]

> Username: Kojoley  
> Created at: 2019-02-28 15:01:03 UTC  
> Updated at: 2019-03-02 19:00:16 UTC  
> Closed at: 2019-03-02 16:02:54 UTC  
> Url: https://github.com/boostorg/math/pull/186  

```  
boost/math/constants/constants.hpp:268:200: warning: extra ';' [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(sixth, 1.666666666666666666666666666666666666e-01, "1.66666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666666667e-01");  
                                                                                                                                                                                                        ^  
boost/math/constants/constants.hpp:304:208: warning: extra ';' [-Wpedantic]  
   BOOST_DEFINE_MATH_CONSTANT(exp_minus_one, 3.678794411714423215955237701614608674e-01, "3.67879441171442321595523770161460867445811131031767834507836801697461495744899803357147274345919643746627325277e-01");  
                                                                                                                                                                                                                ^  
```

---

## Comment 1

> Username: NAThompson  
> Created_at: 2019-02-28 17:39:22 UTC  
> Url: https://github.com/boostorg/math/pull/186#issuecomment-468366160  

What compiler did you find this on?

---

## Comment 2

> Username: Kojoley  
> Created_at: 2019-02-28 17:40:41 UTC  
> Url: https://github.com/boostorg/math/pull/186#issuecomment-468366629  

GCC

---

## Comment 3

> Username: Kojoley  
> Created_at: 2019-02-28 18:02:44 UTC  
> Url: https://github.com/boostorg/math/pull/186#issuecomment-468374499  

`-pedantic`

---

## Comment 4

> Username: NAThompson  
> Created_at: 2019-02-28 18:15:06 UTC  
> Url: https://github.com/boostorg/math/pull/186#issuecomment-468378686  

Ok, it is indeed pretty terrifying to see that pile of warnings.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2019-03-02 16:02:54 UTC  
> Url: https://github.com/boostorg/math/pull/186#issuecomment-468933389  

Thanks for that - those fixes are already in develop so closing down.

---
