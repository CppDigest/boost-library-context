# #1034 - Unexpected exception thrown by `hypergeometric_1F1` [Closed]

> Username: WarrenWeckesser  
> Created at: 2023-10-06 00:38:47 UTC  
> Updated at: 2023-10-13 09:34:21 UTC  
> Closed at: 2023-10-13 09:34:21 UTC  
> Url: https://github.com/boostorg/math/issues/1034  

Calling (for example) `hypergeometric_1F1(13.0, 1.5, 61.0)` throws the exception:  
```  
libc++abi: terminating due to uncaught exception of type std::domain_error: Error in function boost::math::tgamma<long double>(long double): Evaluation of tgamma at a negative integer -1.  
```  
This is on macos 13.6 with Apple clang version 14.0.3 (clang-1403.0.22.14.1).  
  
I'm using the `develop` branch, commit 1722ef9261b51c414b976a156baaf1e4d3ceea66.  
  
My test code is [`hyp1f1.cpp`](https://github.com/WarrenWeckesser/experiments/blob/master/c%2B%2B/boost/hypergeometric_1f1/hyp1f1.cpp).  
  
The exception is not thrown when I change `b` to something that is not exactly 1.5.  For example,  
  
```  
% ./hyp1f1 13 1.499999 61                                                                     
1F1 =   1.35509150477570084e+39  
pFq =   1.35509150509447403e+39  
% ./hyp1f1 13 1.500001 61  
1F1 =   1.35508003684581251e+39  
pFq =   1.35508003682636603e+39  
```  
  
Note that the result computed with `hypergeometric_pFq` is much more accurate in both cases.  The expected values for these calculations can be computed with mpmath in Python:  
  
```  
In [28]: from mpmath import mp  
  
In [29]: mp.dps = 250  
  
In [30]: float(mp.hyp1f1(13.0, 1.499999, 61))  
Out[30]: 1.3550915050944761e+39  
  
In [31]: float(mp.hyp1f1(13.0, 1.500001, 61))  
Out[31]: 1.3550800368263657e+39  
```

---

## Comment 1

> Username: WarrenWeckesser  
> Created at: 2023-10-06 02:59:14 UTC  
> Url: https://github.com/boostorg/math/issues/1034#issuecomment-1749918075  

The exception is thrown in the call of `tgamma` here: https://github.com/boostorg/math/blob/1722ef9261b51c414b976a156baaf1e4d3ceea66/include/boost/math/special_functions/detail/hypergeometric_1F1_bessel.hpp#L594  
  
The exception is thrown when `b - a` is a half-integer and the parameters are sufficiently large (I haven't tried to figure out the exact conditions).  For example,  
  
```  
$ ./hyp1f1 14.25 2.75 54  
terminate called after throwing an instance of 'boost::wrapexcept<std::domain_error>'  
  what():  Error in function boost::math::tgamma<long double>(long double): Evaluation of tgamma at a negative integer -1.  
Aborted (core dumped)  
$ ./hyp1f1 14.125 2.625 100  
terminate called after throwing an instance of 'boost::wrapexcept<std::domain_error>'  
  what():  Error in function boost::math::tgamma<long double>(long double): Evaluation of tgamma at a negative integer -1.  
Aborted (core dumped)  
```

---

## Comment 2

> Username: jzmaddock  
> Created at: 2023-10-10 16:17:35 UTC  
> Url: https://github.com/boostorg/math/issues/1034#issuecomment-1755780442  

Confirmed, looking for the best fix now...
