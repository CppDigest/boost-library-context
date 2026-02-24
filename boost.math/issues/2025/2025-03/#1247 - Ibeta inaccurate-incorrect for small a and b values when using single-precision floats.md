# #1247 - Ibeta inaccurate/incorrect for small a and b values when using single-precision floats [Closed]

> Username: mborland  
> Created at: 2025-03-28 13:53:19 UTC  
> Updated at: 2025-04-18 10:26:52 UTC  
> Closed at: 2025-04-18 10:26:52 UTC  
> Url: https://github.com/boostorg/math/issues/1247  

See: https://github.com/scipy/scipy/issues/22682 for reproducer

---

## Comment 1

> Username: pearu  
> Created at: 2025-03-28 14:34:05 UTC  
> Url: https://github.com/boostorg/math/issues/1247#issuecomment-2761547839  

Just FYI, the corresponding issue in JAX was fixed in https://github.com/jax-ml/jax/pull/27107: see the code around comments " When a is close to zero.." and "For very small a and to avoid division by zero..".
