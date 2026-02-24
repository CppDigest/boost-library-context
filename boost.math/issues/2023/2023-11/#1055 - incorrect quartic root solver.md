# #1055 - incorrect quartic root solver [Closed]

> Username: wztzjhn  
> Created at: 2023-11-26 13:35:31 UTC  
> Updated at: 2023-11-27 16:52:58 UTC  
> Closed at: 2023-11-27 16:52:58 UTC  
> Url: https://github.com/boostorg/math/issues/1055  

Hi,  
I am testing the quartic root solver recently. While in most cases it gives the right answer, I see cases it doesn't do the job right. The following is a simple c++ code to show the error:  
```  
#include <iostream>  
#include <boost/math/tools/quartic_roots.hpp>  
  
int main()  
{  
    const double a = 1.0;  
    const double b = -547.5045576653938;  
    const double c = 75042.069484941996;  
    const double d = 273.7522788326969;  
    const double e = 0.24965766552610175;  
  
    auto res = boost::math::tools::quartic_roots(a, b, c, d, e);  
    std::cout << "solution from Boost:" << std::endl;  
    for (int i = 0; i < 4; ++i) {  
        std::cout << "x[" << i << "] = " << res[i] << std::endl;  
    }  
    std::cout << std::endl;  
  
    double x0M = -0.00182420203946279;  
    double x1M = -0.00182370927680797;  
    std::cout << "solution from Mathematica: " << std::endl;  
    std::cout << "x[0]: " << x0M << std::endl;  
    std::cout << "x[1]: " << x1M << std::endl;  
    std::cout << std::endl;  
  
    std::cout << "check LHS of the quartic equation (with Mathematica solution):" << std::endl;  
  
    std::cout << (((a * x0M + b) * x0M + c) * x0M + d) * x0M + e << std::endl;  
    std::cout << (((a * x1M + b) * x1M + c) * x1M + d) * x1M + e << std::endl;  
  
    return 0;  
}  
```  
The output on my Ubuntu 22.04, with Boost 1.83.0, is:  
```  
solution from Boost:  
x[0] = nan  
x[1] = nan  
x[2] = nan  
x[3] = nan  
  
solution from Mathematica:   
x[0]: -0.0018242  
x[1]: -0.00182371  
  
check LHS of the quartic equation (with Mathematica solution):  
-5.55112e-17  
-2.77556e-17  
```  
  
To see the error more clearly, a Mathematica screenshot is shown below:  
![behavior_mathematica](https://github.com/boostorg/math/assets/5083567/fb122382-7176-4c68-a60e-af85e43137ef)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-11-26 16:44:55 UTC  
> Url: https://github.com/boostorg/math/issues/1055#issuecomment-1826832200  

Confirmed.  
  
@NAThompson the function is failing when we get to here: https://github.com/boostorg/math/blob/f7f96150df7b238537d8f4d52aa27e128f505079/include/boost/math/tools/quartic_roots.hpp#L141  
  
The equation being solved is x^2 + 273.75592674401321x + 18735.576856868389 = 0 which *does* have a root at ~ -136 see : https://www.wolframalpha.com/input?i=solve+x%5E2+%2B+273.75592674401321x+%2B+18735.576856868389+%3D+0  
  
The graph here shows we are very close to missing the axis and having no real root: https://www.wolframalpha.com/input?i=plot+y+%3D+x%5E2+%2B+273.75592674401321x+%2B+18735.576856868389 so this *may* be a case of numerical error (something we will always hit eventually).

---

## Comment 2

> Username: jzmaddock  
> Created at: 2023-11-26 16:51:38 UTC  
> Url: https://github.com/boostorg/math/issues/1055#issuecomment-1826833636  

The discriminant of the quadratic is negative, yet it still has a solution???

---

## Comment 3

> Username: pulver  
> Created at: 2023-11-26 17:24:22 UTC  
> Url: https://github.com/boostorg/math/issues/1055#issuecomment-1826841073  

> The equation being solved is x^2 + 273.75592674401321x + 18735.576856868389 = 0  
  
That polynomial never reaches 0 on the Reals:  
  
https://www.wolframalpha.com/input?i=minimize+x%5E2%2B273.75592674401321x%2B18735.576856868389  
  
When interpreted as exact rational numbers, the minimum value is positive:  
  
![image](https://github.com/boostorg/math/assets/39707/39ca41f6-3a0e-42bb-ae70-eae6508d53f8)

---

## Comment 4

> Username: NAThompson  
> Created at: 2023-11-26 19:47:50 UTC  
> Url: https://github.com/boostorg/math/issues/1055#issuecomment-1826879723  

@jzmaddock : I believe your suspicion of numerical error is correct. That said, `numpy` gets it correct:  
  
```python  
>>> import numpy  
>>> p = numpy.polynomial.Polynomial([0.24965766552610175, 273.7522788326969, 75042.069484941996, -547.5045576653938, 1.0])  
>>> print(p)  
0.24965767 + 273.75227883·x + 75042.06948494·x² - 547.50455767·x³ + 1.0·x⁴  
>>> p.roots()  
array([-1.82420204e-03 +0.j        , -1.82370928e-03 +0.j        ,  
        2.73754103e+02-10.13695958j,  2.73754103e+02+10.13695958j])  
```  
  
Moreover, though these roots are not well separated, the condition number of the rootfinding problem is not _intractably_ large:  
  
```python  
>>> dpdx = p.deriv()  
>>> condition_number = 1/abs(dpdx(-1.82370928e-03))  
>>> condition_number  
27.042429151092385  
```  
  
So there's a chance that we can use some trickery somewhere in the algorithm to improve the precision of some intermediate and recover these roots.  
  
@wztzjhn : Thanks for the report; this appears to be a real problem, though at this point I'm not sure I can guarantee successful resolution. The "correct" way to do this is to use the companion matrix, but this cannot distinguish between real and complex roots, whereas the algorithm employed here always finds the real roots. Will investigate more.

---

## Comment 5

> Username: NAThompson  
> Created at: 2023-11-26 20:07:51 UTC  
> Updated at: 2023-11-26 20:19:35 UTC  
> Url: https://github.com/boostorg/math/issues/1055#issuecomment-1826884116  

Oi, the following diff fixes it:  
  
<img width="865" alt="Screenshot 2023-11-26 at 12 07 02" src="https://github.com/boostorg/math/assets/5459618/4a0852cc-635f-4560-aa74-f372edf616ac">  
  
  
Will submit a fix later today; thanks @wztzjhn , @jzmaddock , and @pulver for triaging and making this so easy for me.  
  
@jzmaddock , @pulver , @mborland : Quick favor: Would one of you cast the coefficients to quad and see what the roots are? (I don't have quadmath on ARM.) I'm still about a million ulps off the Mathematica solution (relative error of 10^-9) and if Mathematica is using double precision under the hood I'm not sure which of us is closer to the truth.
