# #110 - Semantics of pointer returned by allocate_unique() [Closed]

> Username: triplejam  
> Created at: 2024-01-23 14:17:50 UTC  
> Updated at: 2024-01-24 17:33:42 UTC  
> Closed at: 2024-01-24 17:33:42 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/110  

Hi, I have a very simple question about pointers returned by allocate_unique(). It appears you can't get the raw pointer by calling get(), you have to call get().ptr(). I am assuming that this is intentional, but it wasn't obvious in the documentation to me. It's not a big hassle but I 'm wondering why.

---

## Comment 1

> Username: glenfe  
> Created at: 2024-01-24 05:33:52 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/110#issuecomment-1907407858  

@triplejam In the case of arrays of unknown bound, the number of elements is stored in the `pointer` not in the `deleter_type`.   
  
In the case of arrays of unknown bound, i.e. `allocate_unique<T[]>(n)`, the `pointer` type stores an `Allocator::pointer` and a `size_t`.  
  
In the case of scalars, i.e. `allocate_unique<T>(args)` and arrays of known bound, i.e. `allocate_unique<T[N]>()`, the `pointer` type stores just `Allocator::pointer`.  
  
Rather than the first being the only one that uses a wrapper type, it was better for all three to be consistent. See also `get_allocator_ptr`.

---

## Comment 2

> Username: triplejam  
> Created at: 2024-01-24 17:33:42 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/110#issuecomment-1908609730  

Ahhh.... I see! Good feature. Thank you.
