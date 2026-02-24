# #42 - always(reference_wrapper<T>) overload [Closed]

> Username: viboes  
> Created at: 2015-10-04 01:55:05 UTC  
> Updated at: 2015-10-05 05:47:06 UTC  
> Closed at: 2015-10-05 04:44:51 UTC  
> Url: https://github.com/boostorg/hof/issues/42  

`always_ref(v)` could be equivalent to `always(ref(v))`with an specialization for   
  
``` c++  
template<class T>  
always(reference_wrapper<T> value);  
```

---

## Comment 1

> Username: ldionne  
> Created at: 2015-10-04 01:56:32 UTC  
> Url: https://github.com/boostorg/hof/issues/42#issuecomment-145306474  

+1, I think this is a great idea. It sucks to have to include the whole of `<functional>` just for `std::reference_wrapper`, but meh.

---

## Comment 2

> Username: viboes  
> Created at: 2015-10-04 06:58:00 UTC  
> Url: https://github.com/boostorg/hof/issues/42#issuecomment-145323516  

You are right Louis, this is C++ ;-)

---

## Comment 3

> Username: pfultz2  
> Created at: 2015-10-05 04:44:51 UTC  
> Url: https://github.com/boostorg/hof/issues/42#issuecomment-145429113  

`fit::always` already unwraps the reference. `always_ref` is provided because it is used in many places in the library, and it helps reduce down the compile time overhead(it seems unnecessary to always wrap and unwrap the reference).

---

## Comment 4

> Username: viboes  
> Created at: 2015-10-05 05:47:06 UTC  
> Url: https://github.com/boostorg/hof/issues/42#issuecomment-145436142  

this must be precise in the documentation.
