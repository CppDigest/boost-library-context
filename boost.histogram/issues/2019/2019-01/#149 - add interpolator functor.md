# #149 - add interpolator functor [Open]

> Username: HDembinski  
> Created at: 2019-01-21 19:24:54 UTC  
> Updated at: 2020-01-17 09:59:29 UTC  
> Url: https://github.com/boostorg/histogram/issues/149  

People often want to interpolate histograms. Interpolating profiles is even more useful. Interpolation should be done via a functor, so that the interpolation can have state.  
```  
auto h = ... // histogram  
auto interp = interpolator(h, interpolator::linear);  
```

---

## Comment 1

> Username: HDembinski  
> Created at: 2019-01-21 19:40:52 UTC  
> Url: https://github.com/boostorg/histogram/issues/149#issuecomment-456180569  

State may be needed for spline interpolation.

---

## Comment 2

> Username: NAThompson  
> Created at: 2019-08-19 22:24:36 UTC  
> Url: https://github.com/boostorg/histogram/issues/149#issuecomment-522777142  

@HDembinski : There are tons of splines in Boost.Math. Presumably one of any of the functions in `boost/math/interpolators` would do?

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-08-20 08:02:50 UTC  
> Url: https://github.com/boostorg/histogram/issues/149#issuecomment-522902555  

@NAThompson Good point, I see that there were several added in the last two releases. [Catmull-Rom splines](https://www.boost.org/doc/libs/1_71_0/libs/math/doc/html/math_toolkit/catmull_rom.html) and the [vector-valued barycentric rational interpolation](https://www.boost.org/doc/libs/1_71_0/libs/math/doc/html/math_toolkit/barycentric.html) are candidates. Other interpolators only work for 1D data and are not suitable here.  
  
The interpolators should be added in a way that at least linear interpolation can be used without requiring Boost.Math as a dependency.

---

## Comment 4

> Username: HDembinski  
> Created at: 2019-08-20 08:09:11 UTC  
> Url: https://github.com/boostorg/histogram/issues/149#issuecomment-522904639  

On closer look, it seems like neither works. Both interpolate a multi-dimensional point as a function of the 1D variable. A histogram is the opposite. The interpolated value is 1D and the dependent variable is an ND point. So none of the interpolators in Boost.Math can be used.

---

## Comment 5

> Username: HDembinski  
> Created at: 2019-08-20 08:15:26 UTC  
> Url: https://github.com/boostorg/histogram/issues/149#issuecomment-522906846  

Basically, interpolation for Histograms is R^n -> R instead of R -> R^n.

---

## Comment 6

> Username: NAThompson  
> Created at: 2019-08-20 11:09:45 UTC  
> Updated at: 2019-08-20 11:28:50 UTC  
> Url: https://github.com/boostorg/histogram/issues/149#issuecomment-522965780  

R^n->R interpolation is more difficult and it's been in the back of my head to add it for some time. Do you have an arbitrary pointset in R^n or will a uniform grid on compact subsets of R^n be sufficient?   
  
Also, what are reasonable values of n for this problem?

---

## Comment 7

> Username: HDembinski  
> Created at: 2019-08-20 13:41:41 UTC  
> Url: https://github.com/boostorg/histogram/issues/149#issuecomment-523020437  

Depending on the axis type for the histogram, it can be uniform, but in general will be non-uniform. Reasonable values of n are 1 to 6, but can be arbitrarily large in principle.

---

## Comment 8

> Username: HDembinski  
> Created at: 2019-08-20 13:48:50 UTC  
> Url: https://github.com/boostorg/histogram/issues/149#issuecomment-523023376  

I agree, R^n -> R is way more difficult. Only linear interpolation is easy in any dimension, so that's definitely the first algorithm that will be implemented. I spend a lot of time reading up on fast interpolation a few years back for another project. I can recommend the famous Numerical Recipes http://numerical.recipes on the subject. The references in the SciPy docs are also helpful. https://docs.scipy.org/doc/scipy/reference/interpolate.html

---

## Comment 9

> Username: NAThompson  
> Created at: 2020-01-03 18:32:50 UTC  
> Url: https://github.com/boostorg/histogram/issues/149#issuecomment-570658131  

I think that the Makima spline might be the way to go for this:  
  
https://blogs.mathworks.com/cleve/2019/04/29/makima-piecewise-cubic-interpolation/

---

## Comment 10

> Username: HDembinski  
> Created at: 2020-01-16 08:38:47 UTC  
> Url: https://github.com/boostorg/histogram/issues/149#issuecomment-575043908  

Thank you for this interesting blog post, I didn't know about the pchip and the makima methods, which both look very interesting.

---

## Comment 11

> Username: HDembinski  
> Created at: 2020-01-16 08:40:02 UTC  
> Url: https://github.com/boostorg/histogram/issues/149#issuecomment-575044388  

pchip is interesting, because we often do not want the interpolation to leave the value range of the data, so example, interpolated values in a histogram should not become less than zero.

---

## Comment 12

> Username: NAThompson  
> Created at: 2020-01-16 14:31:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/149#issuecomment-575177428  

The main reason this is a good method is that it has a natural generalization to higher dimensions; see Akima's 1974 paper: https://doi.org/10.1145/360767.360779

---

## Comment 13

> Username: HDembinski  
> Created at: 2020-01-17 09:59:28 UTC  
> Url: https://github.com/boostorg/histogram/issues/149#issuecomment-575557609  

Yes, that is definitely interesting.
