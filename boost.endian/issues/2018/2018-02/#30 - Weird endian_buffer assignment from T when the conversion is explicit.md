# #30 - Weird endian_buffer assignment from T when the conversion is explicit [Closed]

> Username: viboes  
> Created at: 2018-02-25 15:03:41 UTC  
> Updated at: 2019-04-29 16:36:26 UTC  
> Closed at: 2019-04-29 16:36:26 UTC  
> Url: https://github.com/boostorg/endian/issues/30  

Wiven that the conversion from T is explicit, the assignment shouldn't be allowed.  
  
```c++  
      explicit endian_buffer(T v) noexcept;  
  
      endian_buffer& operator=(T v) noexcept;  
```  
  
Either the conversion is implicit and the assignment is an optimization, or the conversion is explicit and the assignment should be removed.

---

## Comment 1

> Username: viboes  
> Created at: 2018-02-25 15:16:41 UTC  
> Url: https://github.com/boostorg/endian/issues/30#issuecomment-368317330  

Note that `endian_arithmetic` is already implicitly constructible from `T`.   
Why `endian_arithmetic` implicitly constructible from `T` while `endian_buffer` is not?

---

## Comment 2

> Username: pdimov  
> Created at: 2019-04-29 16:36:26 UTC  
> Url: https://github.com/boostorg/endian/issues/30#issuecomment-487651613  

This all seems to be by design. `endian_buffer` has no implicit conversions, `endian_arithmetic` does.
