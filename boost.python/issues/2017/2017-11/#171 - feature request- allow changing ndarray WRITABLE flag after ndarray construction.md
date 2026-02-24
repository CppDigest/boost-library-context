# #171 - feature request: allow changing ndarray WRITABLE flag after ndarray construction [Open]

> Username: HDembinski  
> Created at: 2017-11-01 13:46:53 UTC  
> Updated at: 2018-08-03 20:46:10 UTC  
> Url: https://github.com/boostorg/python/issues/171  

I want to write a function which creates, fills, and returns an ndarray in C++. The ndarray owns its memory, but should be non-writable for the user. I would therefore like to clear the WRITABLE flag after I finished filling it in my function. I studied the header and I see no way of doing this right now, because flags are not writable after creating an ndarray. For most flags this makes sense, the WRITABLE flag seems to be the only one which you may want to change manually.  
  
Is it possible to add this feature? Best regards!

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-08-01 09:07:23 UTC  
> Url: https://github.com/boostorg/python/issues/171#issuecomment-409506470  

Any chance that this will be implemented? Would you review a PR if I prepare one?

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2018-08-01 10:11:16 UTC  
> Url: https://github.com/boostorg/python/issues/171#issuecomment-409524657  

Yes, it looks like a simple `ndarray::set_flags()` method is missing, right ?

---

## Comment 3

> Username: HDembinski  
> Created at: 2018-08-01 10:22:59 UTC  
> Updated at: 2018-08-01 10:23:36 UTC  
> Url: https://github.com/boostorg/python/issues/171#issuecomment-409527741  

This would be the generic solution, right, but it may open possibilities to wreck havoc with the internal consistency of the array. We could document this with a "use at your own peril" or something.  
  
When I look through the list of flags of the C-API  
https://docs.scipy.org/doc/numpy/reference/c-api.array.html#c.NPY_ARRAY_C_CONTIGUOUS  
I think only `NPY_ARRAY_WRITEABLE` should ever be changed after construction of the array. So perhaps a method `ndarray::set_writeable(bool on)` is sufficient.

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2018-08-01 10:25:59 UTC  
> Url: https://github.com/boostorg/python/issues/171#issuecomment-409528459  

I'd rather not hand-hold users of the API. So if this is possible in the NumPy C API, I'm inclined to make it accessible in Boost.Python directly. But of course, proper documentation (including warning signs) would be helpful.

---

## Comment 5

> Username: HDembinski  
> Created at: 2018-08-01 10:28:31 UTC  
> Url: https://github.com/boostorg/python/issues/171#issuecomment-409529110  

Ok!

---

## Comment 6

> Username: HDembinski  
> Created at: 2018-08-03 20:46:10 UTC  
> Url: https://github.com/boostorg/python/issues/171#issuecomment-410371689  

I submitted a PR. Some tests are failing, but this seems unrelated to the new code...
