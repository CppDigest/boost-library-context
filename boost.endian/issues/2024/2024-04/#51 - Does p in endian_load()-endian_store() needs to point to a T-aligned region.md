# #51 - Does p in endian_load()/endian_store() needs to point to a T-aligned region? [Open]

> Username: manipuladordedados  
> Created at: 2024-04-04 19:51:15 UTC  
> Updated at: 2024-04-04 23:56:02 UTC  
> Url: https://github.com/boostorg/endian/issues/51  

Documentation doesn't state this requirement clearly. Furthermore, these functions are part of the header conversion.hpp for which documentation states that alignment is required. I think it's correct to assume that T is aligned, but not p (otherwise I'd have to create an extra buffer and perform an extra memcpy for no good reason). What is the requirement for these functions?

---

## Comment 1

> Username: pdimov  
> Created at: 2024-04-04 23:56:01 UTC  
> Url: https://github.com/boostorg/endian/issues/51#issuecomment-2038468419  

No, `p` doesn't need to be aligned. Unless otherwise specified, a pointer to `unsigned char` doesn't have alignment requirements (because the alignment of `unsigned char` is always 1.)  
  
I'll make this explicit in the documentation.
