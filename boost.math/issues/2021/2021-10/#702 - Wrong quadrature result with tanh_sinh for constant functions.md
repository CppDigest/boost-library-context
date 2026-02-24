# #702 - Wrong quadrature result with tanh_sinh for constant functions [Closed]

> Username: Robert-van-Engelen  
> Created at: 2021-10-09 21:05:29 UTC  
> Updated at: 2021-10-11 16:53:34 UTC  
> Closed at: 2021-10-11 16:53:34 UTC  
> Url: https://github.com/boostorg/math/issues/702  

I would expect the result to be 40 when integrating a constant function f(x)=2 over [-10,10], but 20 is returned:  
~~~  
auto f = [&](double x) { return 2; };  
static const double tol = 1E-9;  
double Q = tanh_sinh<double>().integrate(f, -10.0, 10.0, tol, &error);  
~~~  
Also f(x)=2 over [0,10] returns 10 when the area is 20.  
  
I upgraded to Boost 1.76.

---

## Comment 1

> Username: NAThompson  
> Created at: 2021-10-09 22:39:47 UTC  
> Url: https://github.com/boostorg/math/issues/702#issuecomment-939371223  

Confirmed.  
  
Haven't looked into the cause, but have determined that if you use `return 2.0`, then the problem goes away. I expect that when we used the return-type deduction for the complex valued quadrature, we get a type `K=int` on this example.  
  
Full example for reproduction:  
  
```  
#include <iostream>  
#include <boost/math/quadrature/tanh_sinh.hpp>  
  
using boost::math::quadrature::tanh_sinh;  
  
int main() {  
    auto f = [&](double x) { return 2.0; };  
    static const double tol = 1E-9;  
    double error;  
    double Q = tanh_sinh<double>().integrate(f, -10.0, 10.0, tol, &error);  
    std::cout << "Q  = " << Q << " with error " << error << "\n";  
}  
```

---

## Comment 2

> Username: NAThompson  
> Created at: 2021-10-10 17:53:50 UTC  
> Updated at: 2021-10-10 18:25:29 UTC  
> Url: https://github.com/boostorg/math/issues/702#issuecomment-939524352  

Fixed in [704](https://github.com/boostorg/math/pull/704), though the fix is just a static assert on the return type.  
  
At least that particular footgun is eliminated. Thanks for the report.
