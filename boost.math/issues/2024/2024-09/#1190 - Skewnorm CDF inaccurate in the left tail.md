# #1190 - Skewnorm CDF inaccurate in the left tail [Open]

> Username: dschmitz89  
> Created at: 2024-09-01 15:33:04 UTC  
> Updated at: 2026-01-27 09:28:17 UTC  
> Url: https://github.com/boostorg/math/issues/1190  

SciPy currently falls back to quadrature integration of the PDF in the left tail of the skewnorm CDF for positive $a$ as the boost function has issues for low values. There seem to be catastrophic cancellations resulting in CDF values of 0. See the python example below. This is probably not a super serious issue but still wanted to make you aware in case you would like to fix it on your end.  
  
  
```python  
  
import numpy as np  
from scipy import special  
import matplotlib.pyplot as plt  
  
a = 1  
x = np.linspace(-50, 0, 100000)  
  
plt.semilogy(x, special._ufuncs._skewnorm_cdf(x, 0.0, 1.0, a))  
plt.show()  
  
```   
  
Which results in the following plot:  
  
![image](https://github.com/user-attachments/assets/aea9dd8b-5f39-416b-ba46-4fbf203b23b5)  
  
Zooming in shows   
  
![image](https://github.com/user-attachments/assets/2a34ba89-064e-403f-8ed3-38742be46386)  
  
For reference what the parameters of `_skewnorm_cdf` mean here the wrapper code:  
  
```c++  
  
template<typename Real>  
Real  
skewnorm_cdf_wrap(const Real x, const Real l, const Real sc, const Real sh)  
{  
    if (std::isfinite(x)) {  
        return boost::math::cdf(  
            boost::math::skew_normal_distribution<Real, StatsPolicy>(l, sc, sh), x);  
    }  
    // -inf => 0, inf => 1  
    return 1 - std::signbit(x);  
}  
  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-09-03 12:23:22 UTC  
> Url: https://github.com/boostorg/math/issues/1190#issuecomment-2326388737  

I'll need to re-read the papers to see if there's a better way, although interestingly for shape=1 the cdf appears to simply be the square of the normal cdf which would at least solve that specific case.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2024-09-04 19:22:42 UTC  
> Url: https://github.com/boostorg/math/issues/1190#issuecomment-2329807125  

Notes to self, since I'm about to disappear on holiday ;)  
  
Method T6 from "FAST AND ACCURATE CALCULATION OF OWEN’S T-FUNCTION" is interesting, but fails to provide an error bound.  
  
"Evaluating the CDF of the Skew Normal Distribution", Amsler,2020 provides some interesting approximations for the far tails.

---

## Comment 3

> Username: weibo-gao  
> Created at: 2026-01-27 09:28:17 UTC  
> Url: https://github.com/boostorg/math/issues/1190#issuecomment-3804086276  

The root cause in boost/math/distributions/skew_normal.hpp is the direct subtraction cdf(normal, x) - 2 * owens_t(x, alpha).When $x$ is large and $\alpha > 0$, both terms approach $0.5$ (or values that cancel out), leading to catastrophic cancellation.In my SciPy tests (scipy/scipy#24439), for $x=30, \alpha=5$, the current implementation returns $0$. However, using the alternative symmetry property $T(h, a) = \frac{1}{2}\Phi(h) + \frac{1}{2}\Phi(ah) - \Phi(h)\Phi(ah) - T(ah, 1/a)$ or direct tail expansions could bypass this subtraction
