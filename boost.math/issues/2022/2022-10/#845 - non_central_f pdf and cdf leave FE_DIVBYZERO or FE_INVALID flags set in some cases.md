# #845 - non_central_f pdf and cdf leave FE_DIVBYZERO or FE_INVALID flags set in some cases. [Closed]

> Username: WarrenWeckesser  
> Created at: 2022-10-16 03:33:56 UTC  
> Updated at: 2022-10-17 20:52:57 UTC  
> Closed at: 2022-10-17 20:52:57 UTC  
> Url: https://github.com/boostorg/math/issues/845  

Suppose `ncf` is created with  
```  
    auto ncf = non_central_f(v1, v2, lambda);  
```  
If `v1 + v2` is exactly 4 and `lambda` is not too big, a call of the form  
```  
    cdf(ncf, x)  
```  
leaves one of the floating point flags `FE_DIVBYZERO` or `FE_INVALID` set.  `FE_INVALID` occurs when `v1` and `v2` are both 2; otherwise `FE_DIVBYZERO` is set.  
  
Also, if `v1 + v2` is exactly 2, then both the `cdf` and `pdf` functions leave the `FE_DIVBYZERO` flag set.  
  
The return values (as far as I've checked them) appear to be correct.  
  
I used the following code to check the flags.  
  
<details>  
<summary>  
Demo code  
</summary>  
  
```  
#include <string>  
#include <iostream>  
#include <cfenv>  
#include <cmath>  
#include <boost/math/distributions/non_central_f.hpp>  
  
using namespace std;  
using boost::math::non_central_f;  
  
  
void show_fp_exception_flags()  
{  
    if (std::fetestexcept(FE_DIVBYZERO)) {  
        cout << " FE_DIVBYZERO";  
    }  
    // FE_INEXACT is common and not interesting.  
    // if (std::fetestexcept(FE_INEXACT)) {  
    //     cout << " FE_INEXACT";  
    // }  
    if (std::fetestexcept(FE_INVALID)) {  
        cout << " FE_INVALID";  
    }  
    if (std::fetestexcept(FE_OVERFLOW)) {  
        cout << " FE_OVERFLOW";  
    }  
    if (std::fetestexcept(FE_UNDERFLOW)) {  
        cout << " FE_UNDERFLOW";  
    }  
    cout << endl;  
}  
  
int main(int argc, char *argv[])  
{  
    if (argc != 5 ) {  
        printf("use: %s x v1 v2 lambda\n", argv[0]);  
        return -1;  
    }  
  
    double x = std::stod(argv[1]);  
    double v1 = std::stod(argv[2]);  
    double v2 = std::stod(argv[3]);  
    double lambda = std::stod(argv[4]);  
  
    auto ncf = non_central_f(v1, v2, lambda);  
  
    std::feclearexcept(FE_ALL_EXCEPT);  
    double p = pdf(ncf, x);  
    cout << "fp flags after pdf():";  
    show_fp_exception_flags();  
  
    std::feclearexcept(FE_ALL_EXCEPT);  
    double c = cdf(ncf, x);  
    cout << "fp flags after cdf():";  
    show_fp_exception_flags();  
  
    cout << "pdf: " << setw(24) << setprecision(16) << p << endl;  
    cout << "cdf: " << setw(24) << setprecision(16) << c << endl;  
  
    return 0;  
}  
```  
</details>  
  
I suspect the problem is in the implementation of the noncentral beta distribution, which is called by the `non_central_f` functions with `a = v1/2` and `b = v2/2`.  In particular, in this line  
  
https://github.com/boostorg/math/blob/aa242e5639b61e95d10f53974a0d4285e800249e/include/boost/math/distributions/non_central_beta.hpp#L89  
  
if `i` is 0 and `a + b` is 2 (which corresponds to `v1 + v2 = 4`) , the denominator is 0.  
  
These flags cause a problem in the SciPy wrappers of the pdf and cdf functions.  The wrappers use the NumPy "ufunc" machinery, which automatically raises a Python exception when the underlying kernel function returns with either the `FE_DIVBYZERO` or `FE_INVALID` flag set.  We can work around that, but I don't think these functions should leave those flags set when the result is correct, should they?

---

## Comment 1

> Username: mborland  
> Created at: 2022-10-16 15:01:41 UTC  
> Url: https://github.com/boostorg/math/issues/845#issuecomment-1279986850  

@WarrenWeckesser I'll poke around on this one today.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2022-10-16 15:22:33 UTC  
> Url: https://github.com/boostorg/math/issues/845#issuecomment-1279991082  

Confirmed, the next value of xterm is computed at the end of the loop (which is more convenient), but is not used to the start of the next loop, so the last value computed is discarded and it's that which is causing the issue.

---

## Comment 3

> Username: mborland  
> Created at: 2022-10-16 16:26:38 UTC  
> Url: https://github.com/boostorg/math/issues/845#issuecomment-1280002935  

Should be fixed by https://github.com/boostorg/math/pull/846.
