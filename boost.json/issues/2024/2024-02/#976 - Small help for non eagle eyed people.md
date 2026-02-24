# #976 - Small help for non eagle eyed people [Closed]

> Username: RoyBellingan  
> Created at: 2024-02-01 03:39:32 UTC  
> Updated at: 2024-02-08 18:41:22 UTC  
> Closed at: 2024-02-08 18:41:22 UTC  
> Url: https://github.com/boostorg/json/issues/976  

In   
  
https://github.com/boostorg/json/blob/db92f8c22360990f450fe27b86ea1a5830b5cf05/include/boost/json/detail/value_to.hpp#L404  
  
Is possible to add   
```CPP  
static_assert(std::is_default_constructible<T>::value, "The destination type must be default constructible");  
```  
  
As the error generated contains quite a lot of cruft ? And this should help a bit.

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-02-01 08:27:00 UTC  
> Url: https://github.com/boostorg/json/issues/976#issuecomment-1920759507  

Alternatively, I should have made default constructibility a requirement for is_described_class.

---

## Comment 2

> Username: grisumbras  
> Created at: 2024-02-08 13:05:04 UTC  
> Url: https://github.com/boostorg/json/issues/976#issuecomment-1934084623  

Actually, no, I should indeed use a static assert.
