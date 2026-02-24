# #1198 - Noncentral F distribution CDF edge cases [Closed]

> Username: dschmitz89  
> Created at: 2024-09-13 09:57:55 UTC  
> Updated at: 2025-04-14 11:17:55 UTC  
> Closed at: 2025-04-14 11:17:55 UTC  
> Url: https://github.com/boostorg/math/issues/1198  

SciPy is thanks to Boost finally able to remove parts of the ancient cdflib Fortran library. Recently, the noncentral F distribution CDF was  fully replaced by the boost implementation in https://github.com/scipy/scipy/pull/21505, resulting in a far more accurate results for a wide range of inputs. Still, edge cases were uncovered which we would like to share with you. They occur for very large and very tiny values for the degrees of freedom. Note that for the second case even Mathematica is not able to compute a result, so maybe we do not need to thrive for a feasible value but raise an error or return nan. @mdhaber found a limiting formula that might be helpful: https://github.com/scipy/scipy/pull/21505#issuecomment-2334793846  
  
| v1| v2| l | x | Boost result |  
|--------|--------|--------|--------|--------|  
| 1e-100| 3 | 1.5 | 1e100 | nan |  
| 1e20| 1e20 | 1 | 1 | -124.40303872682864 |

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-09-13 18:06:41 UTC  
> Url: https://github.com/boostorg/math/issues/1198#issuecomment-2349721338  

Reproduced, first one is an easy fix.  Not so sure about the second yet...

---

## Comment 2

> Username: jzmaddock  
> Created at: 2024-10-03 18:47:17 UTC  
> Url: https://github.com/boostorg/math/issues/1198#issuecomment-2392094717  

Update: working on this now, there are some easy to fix numerical-stability issues which sort of fix both, but ideally we need some asymptotic expansions for the incomplete beta with large parameters: these exist, and I have them somewhat working, but they are deeply numerically unstable, especially near the saddle point.  Hopefully more soon...

---

## Comment 3

> Username: dschmitz89  
> Created at: 2024-11-21 11:45:01 UTC  
> Url: https://github.com/boostorg/math/issues/1198#issuecomment-2490901434  

FYI: We found a likely related edge for the noncentral t distribution CDF in https://github.com/scipy/scipy/pull/21728#issuecomment-2482130025 .  
  
For `df=980, noncentrality=38, x=1.5` boost computes a negative value of the order of `-1e-70`.
