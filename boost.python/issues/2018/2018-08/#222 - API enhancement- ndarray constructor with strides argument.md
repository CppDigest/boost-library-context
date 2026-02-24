# #222 - API enhancement: ndarray constructor with strides argument [Open]

> Username: HDembinski  
> Created at: 2018-08-01 09:14:16 UTC  
> Updated at: 2018-08-01 10:05:54 UTC  
> Url: https://github.com/boostorg/python/issues/222  

Short: Please add versions of the functions `zeros` and `empty` that accept a `strides` argument.  
  
Long:  
The C++ API of `ndarray` does not allow to create an `ndarray` with the functions `zeros` or `empty` with custom strides. I have a use case in [histogram](https://github.com/HDembinski/histogram) where I need custom strides. This is possible with the underlying C-API, so it is merely an extension of the C++ front-end.  
  
I cannot use `from_data`, because I want the ndarray to own the memory.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-08-01 09:14:48 UTC  
> Url: https://github.com/boostorg/python/issues/222#issuecomment-409508790  

I would be happy to prepare a PR if it gets reviewed.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2018-08-01 10:05:54 UTC  
> Url: https://github.com/boostorg/python/issues/222#issuecomment-409523291  

Sure, I would appreciate that !
