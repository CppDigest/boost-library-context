# #162 - Complex Newton's method [Closed]

> Username: NAThompson  
> Created at: 2018-11-19 20:36:17 UTC  
> Updated at: 2018-12-02 18:33:25 UTC  
> Closed at: 2018-12-02 18:33:25 UTC  
> Url: https://github.com/boostorg/math/issues/162  

Newton's method often works in the complex plane, but the current version of `newton_raphson` only supports real types. I propose adding a function `complex_newton` which works for complex types. I have tested that the following code works reasonably well:  
  
```cpp  
template<class Complex, class F>  
Complex complex_newton(F g, Complex guess,  size_t iterations=30)  
{  
    typedef typename Complex::value_type Real;  
    Complex xn = guess;  
  
    Complex f;  
    Complex df;  
    do {  
  
       auto pair = g(xn);  
       f = pair.first;  
       df = pair.second;  
  
       --iterations;  
  
       xn = xn  - (f/df);  
       if (abs(f) < sqrt(std::numeric_limits<Real>::epsilon()))  
       {  
          return xn;  
       }  
  
    } while(iterations);  
  
    if (abs(f) > std::numeric_limits<typename Complex::value_type>::epsilon())  
    {  
        return {std::numeric_limits<typename Complex::value_type>::quiet_NaN(), std::numeric_limits<typename Complex::value_type>::quiet_NaN()};  
    }  
    return xn;  
}  
```  
  
For instance, it recovers all the symlet and Daubechies coefficients via Maehly's procedure. However, it's not quite ready for primetime since it doesn't handle zero derivatives.

---

## Comment 1

> Username: NAThompson  
> Created at: 2018-12-02 18:33:25 UTC  
> Url: https://github.com/boostorg/math/issues/162#issuecomment-443530976  

Now in [PR 165](https://github.com/boostorg/math/pull/165).
