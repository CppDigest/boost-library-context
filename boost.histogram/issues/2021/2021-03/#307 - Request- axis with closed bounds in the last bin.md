# #307 - Request: axis with closed bounds in the last bin [Closed]

> Username: jolopezl  
> Created at: 2021-03-08 01:11:39 UTC  
> Updated at: 2021-09-30 12:59:28 UTC  
> Closed at: 2021-09-30 12:59:28 UTC  
> Url: https://github.com/boostorg/histogram/issues/307  

(See: https://gitter.im/boostorg/histogram?at=6040fcdc457d6b4a94a1e3be)  
  
I wonder if there is a proper way to set the last bin of a histogram to have a closed bound? It has some statistical effects and applications when analyzing images. Maybe we can foresee a possible way to set a switch for this?  
  
The axis would look like this: `[x_0),x_1),...),x_i),...,x_N-1),x_N]`, instead of `[x_0),x_1),...),x_i),...,x_N-1),x_N)`.

---

## Comment 1

> Username: henryiii  
> Created at: 2021-03-08 04:03:03 UTC  
> Url: https://github.com/boostorg/histogram/issues/307#issuecomment-792445983  

This is pretty easy to implement if you need it, see https://github.com/scikit-hep/boost-histogram/blob/6170cff72d65e63c1f4ad0504b3dab16152cec6b/include/bh_python/regular_numpy.hpp .
