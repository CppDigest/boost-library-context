# #896 - basic_fields class does not check if Allocator supplies fancy pointers [Closed]

> Username: djarek  
> Created at: 2017-11-18 19:34:25 UTC  
> Updated at: 2017-11-21 01:14:01 UTC  
> Closed at: 2017-11-21 01:14:01 UTC  
> Url: https://github.com/boostorg/beast/issues/896  

basic_fields should contain a static_assert to see if the supplied Allocator uses standard pointer types, because it is likely to break for fancy pointers.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-18 19:46:06 UTC  
> Url: https://github.com/boostorg/beast/issues/896#issuecomment-345466311  

`std::is_pointer<typename std::allocator_traits<A>::pointer_type>::value`?

---

## Comment 2

> Username: djarek  
> Created at: 2017-11-18 19:52:45 UTC  
> Url: https://github.com/boostorg/beast/issues/896#issuecomment-345466771  

Should work (A must be the rebound allocator).

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-11-20 20:11:35 UTC  
> Url: https://github.com/boostorg/beast/issues/896#issuecomment-345816640  

What about for all the other classes that use allocators? For example `basic_multi_buffer`?
