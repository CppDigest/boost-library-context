# #407 - support fancy pointers [Closed]

> Username: vinniefalco  
> Created at: 2017-06-01 17:26:58 UTC  
> Updated at: 2019-05-29 19:41:54 UTC  
> Closed at: 2019-05-29 19:41:54 UTC  
> Url: https://github.com/boostorg/beast/issues/407  

Allocator-aware containers should use `pointer_traits` and this utility (part of Boost now I think)  
  
```  
template <class Ptr>  
typename pointer_traits<Ptr>::element_type* to_address(Ptr p) noexcept  
{  
return to_address(p.operator‐>());  
}  
template <class T>  
T* to_address(T* p) noexcept  
{  
return p;  
}  
```

---

## Comment 1

> Username: glenfe  
> Created at: 2017-06-01 18:56:35 UTC  
> Url: https://github.com/boostorg/beast/issues/407#issuecomment-305587047  

`#include <boost/core/pointer_traits.hpp>`  
  
Given a pointer `p` of type `P` you can use the following to get a raw pointer:  
  
`boost::pointer_traits<P>::to_address(p)`  
  
e.g.  
  
```cpp  
P pointer = allocator.allocate(size);  
new(boost::pointer_traits<P>::to_address(pointer)) T(std::forward<Args>(args)...);  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-01 20:12:16 UTC  
> Url: https://github.com/boostorg/beast/issues/407#issuecomment-305606837  

Don't forget to add a test to make sure it actually works.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-05-29 19:21:35 UTC  
> Url: https://github.com/boostorg/beast/issues/407#issuecomment-497074958  

I would like to make `basic_fields` an ordinary class instead of a class template, and supporting fancy pointers directly would complicate that. No one is asking for fancy pointers, and there is no strong case for the benefits relative to the cost.
