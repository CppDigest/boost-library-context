# #143 - stride units in numpy::from_data [Open]

> Username: stefanseefeld  
> Created at: 2017-08-03 15:54:53 UTC  
> Updated at: 2017-08-03 16:11:48 UTC  
> Url: https://github.com/boostorg/python/issues/143  

Answering https://github.com/boostorg/python/issues/142, I notice that the `np::from_data` functions require strides to be counted in units of bytes, rather than elements. Is there any particular reason for that choice ? Given that the function also takes a `dtype` argument, which provides a `get_itemsize()` method, it seems plausible (and a bit more convenient) for callers to not have to use `sizeof(value_type)` explicitly.  
  
@TallJimbo, any thoughts ?

---

## Comment 1

> Username: TallJimbo  
> Created at: 2017-08-03 16:06:31 UTC  
> Url: https://github.com/boostorg/python/issues/143#issuecomment-320015109  

NumPy's internal API uses strides in units of bytes, and I've slowly come around (even in my C++ library for multidimensional arrays, which didn't) to that being the best approach.  That's because it's the only way to really support `reinterpret_cast`-style views into arrays, which are rare but nevertheless important (e.g. interpreting a `complex<double>` array as a `double` array, or unpacking the bytes of an `uint64` that's used to represent a series of packed bits).  
  
Needing to use bytes internally doesn't preclude having some functions that let users specify strides in units of the data type rather than bytes, but I worry that having different conventions in different places would lead to confusion.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2017-08-03 16:09:08 UTC  
> Url: https://github.com/boostorg/python/issues/143#issuecomment-320015853  

Are there any places in the API where strides are passed without the dtype being known (either implicitly or explicitly) ?

---

## Comment 3

> Username: TallJimbo  
> Created at: 2017-08-03 16:11:48 UTC  
> Url: https://github.com/boostorg/python/issues/143#issuecomment-320016587  

I don't think so.
