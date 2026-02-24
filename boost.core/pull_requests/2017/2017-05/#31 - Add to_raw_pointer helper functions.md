# #31 Add to_raw_pointer helper functions [Closed]

> Username: glenfe  
> Created at: 2017-05-10 04:40:13 UTC  
> Updated at: 2017-05-17 13:23:25 UTC  
> Closed at: 2017-05-17 13:22:23 UTC  
> Url: https://github.com/boostorg/core/pull/31  

We have more than one implementation of this in Boost now, and I plan to use it in one more library.  It seems generally useful for Boost library authors supporting Allocators with fancy pointers.

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-05-10 08:43:27 UTC  
> Url: https://github.com/boostorg/core/pull/31#issuecomment-300416023  

Isn't `get_pointer` (`boost/get_pointer.hpp`) supposed to do that? I know it doesn't currently support arbitrary types but it's been there for decades and it could be extended.  
  
Also, the standard solution to this is `&*ptr`. If we do need such function, I think the generic version should follow that.

---

## Comment 2

> Username: glenfe  
> Created_at: 2017-05-10 10:40:32 UTC  
> Updated_at: 2017-05-10 10:46:14 UTC  
> Url: https://github.com/boostorg/core/pull/31#issuecomment-300444172  

@Lastique The reason this is used instead of `addressof(*p)` is because that isn't well defined if there is no object constructed in `p`. Hinnant switched libc++ to use this technique, and I observe that in the cases where libstdc++ support fancy pointers from allocators, that Wakely does the same thing.

---

## Comment 3

> Username: glenfe  
> Created_at: 2017-05-10 10:49:21 UTC  
> Url: https://github.com/boostorg/core/pull/31#issuecomment-300445967  

I like the idea of updating `get_pointer` if Peter is also in agreement.

---

## Comment 4

> Username: pdimov  
> Created_at: 2017-05-11 15:43:54 UTC  
> Url: https://github.com/boostorg/core/pull/31#issuecomment-300830906  

How would the specification of `to_raw_pointer` look like?

---

## Comment 5

> Username: glenfe  
> Created_at: 2017-05-11 16:53:37 UTC  
> Updated_at: 2017-05-11 16:54:14 UTC  
> Url: https://github.com/boostorg/core/pull/31#issuecomment-300850703  

Something like: The argument must be an object of a pointer-like type `T` (such that `pointer_traits<T>::pointer` is valid) that is not a null pointer. The result is a pointer to the `element_type` (that is `pointer_traits<T>::element_type*`) that is suitable for construction with placement new, or `allocator_traits::construct()`?

---

## Comment 6

> Username: pdimov  
> Created_at: 2017-05-11 17:22:55 UTC  
> Url: https://github.com/boostorg/core/pull/31#issuecomment-300858506  

Something like that, yes. Could mention that the result references the same location as the argument, and that the opposite operation is `pointer_to`.

---

## Comment 7

> Username: glenfe  
> Created_at: 2017-05-17 13:23:25 UTC  
> Url: https://github.com/boostorg/core/pull/31#issuecomment-302088573  

Instead we will now have a pointer_traits implementation that provides to_address.

---
