# #193 - Incorrect result associated laguerre function [Closed]

> Username: JFJJanssen  
> Created at: 2019-04-04 10:35:25 UTC  
> Updated at: 2019-04-06 07:27:41 UTC  
> Closed at: 2019-04-06 07:27:41 UTC  
> Url: https://github.com/boostorg/math/issues/193  

While debugging a program I noticed that the reason for the incorrect results are caused by incorrect output from the associated laguerre functions. Most of the results I get are correct (verified with MATLAB output), but for specific values of n and m the results are wrong (laguerre(n, m, x)). I noticed that in the test_laguerre function only positive values of n and m are considered. The incorrect results are produced with negative m values.   
  
For example:  
laguerre(2, -2, 5) = -1.07374e10 while the correct result is 12.5  
laguerre(2, -1, 5) = 7.5 which is correct  
laguerre(3, -3, 5) = -6.14891e18 while the correct result is -20.8333  
laguerre(3, -2, 5) = 7.15828e9 while the correct result is -8.3333  
laguerre(3, -1, 5) = -0.83333 which is correct  
  
The associated laguerre functions are implemented using a recursive relation based on   
L_{n+1}^m = (C_1 * L_{n}^m - C_2 * L_{n-1}^m)/C_3

---

## Comment 1

> Username: ckormanyos  
> Created at: 2019-04-05 21:23:56 UTC  
> Url: https://github.com/boostorg/math/issues/193#issuecomment-480426655  

The first step is to reproduce this phenomenon. @JFJJansen,  
do you happen to know which version of Boost and which  
compiler were used in this test? I'll try to find it in 1.69 with  
VC and go from there.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-04-06 07:27:41 UTC  
> Url: https://github.com/boostorg/math/issues/193#issuecomment-480482072  

The signature for the function is:  
  
```  
template <class T>  
calculated-result-type laguerre(unsigned n, unsigned m, T x);  
```  
  
Note the `unsigned` argument!  Which is to say we followed C++ TR1 spec. and only supported n,m > 0.
