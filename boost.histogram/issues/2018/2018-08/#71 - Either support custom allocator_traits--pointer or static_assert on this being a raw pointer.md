# #71 - Either support custom allocator_traits::pointer or static_assert on this being a raw pointer [Closed]

> Username: HDembinski  
> Created at: 2018-08-28 22:46:50 UTC  
> Updated at: 2018-09-02 14:48:31 UTC  
> Closed at: 2018-09-02 14:47:46 UTC  
> Url: https://github.com/boostorg/histogram/issues/71  

adaptive_storage assumes that `allocator_traits<T>::pointer` is equal to `T*`. To support this properly  
- boost::address_of can be used to convert pointer to T*  
- a instance of pointer needs to be kept around until deallocate on T* is called

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-08-30 08:31:07 UTC  
> Url: https://github.com/boostorg/histogram/issues/71#issuecomment-417235889  

Pointers can be cheaply held in a union.

---

## Comment 2

> Username: HDembinski  
> Created at: 2018-09-02 14:47:46 UTC  
> Updated at: 2018-09-02 14:48:31 UTC  
> Url: https://github.com/boostorg/histogram/issues/71#issuecomment-417935932  

To keep the pointers around safely, they need to be managed in a boost::variant, because the constructors and destructors may be non-trivial. This adds additional complexity, which I don't want to add until it is explicitly requested. There don't seem to be many allocators with custom pointer types around.  
  
adaptive_storage uses a static_assert to check that the allocator uses a trivial pointer type.
