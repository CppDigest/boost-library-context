# #803 - A documentation bug reported for Akima Interpolation [Closed]

> Username: JonKalb  
> Created at: 2022-07-16 07:19:46 UTC  
> Updated at: 2022-10-16 22:55:12 UTC  
> Closed at: 2022-10-16 22:55:12 UTC  
> Url: https://github.com/boostorg/math/issues/803  

owners@boost.org received this from physikqy <zhoujack14@outlook.com>  
  
> Just report a tiny grammer mistake in the example:  
> it should be:  
>   
> auto spline = makima<vector(double)>(std::move(x), std::move(y));  
>   
> just miss <vector(double)>  
> I know this is easy to fix, but for beginners it could be more crystal.  
>   
> https://www.boost.org/doc/libs/master/libs/math/doc/html/math_toolkit/makima.html  
>   
> Best wishes,  
> Qingyuan.  
>

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-07-16 19:21:41 UTC  
> Url: https://github.com/boostorg/math/issues/803#issuecomment-1186271368  

@NAThompson ?

---

## Comment 2

> Username: NAThompson  
> Created at: 2022-07-17 06:55:48 UTC  
> Url: https://github.com/boostorg/math/issues/803#issuecomment-1186423022  

Nah, it should type-deduce right?

---

## Comment 3

> Username: mborland  
> Created at: 2022-07-20 15:35:19 UTC  
> Url: https://github.com/boostorg/math/issues/803#issuecomment-1190438246  

It is type deduced. See test/makima_test.cpp for example.
