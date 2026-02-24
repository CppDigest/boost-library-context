# #766 Fix tiny compile error with clang [Closed]

> Username: shyeyian  
> Created at: 2025-06-16 05:47:21 UTC  
> Updated at: 2025-06-23 15:54:28 UTC  
> Closed at: 2025-06-23 15:54:28 UTC  
> Url: https://github.com/boostorg/gil/pull/766  

`clang++` complains about 2 `template` keywords in `include/boost/gil/algorithm.hpp`.  
  
I'm trying to compile `boost/gil` into a [C++20 module](https://cppreference.com/w/cpp/language/modules.html), where modules are preferred to always be build from source (as `C++20 modules` with different compilation flags cannot be imported from each other), and applying this tiny change will bring great convenience.  
  - compiler: clang++ 20.1.6  
  - args: -std=c++26  
  
```bash  
./include/boost/gil/algorithm.hpp:1241:54: error: a template argument list is expected after a name prefixed by the template keyword [-Wmissing-template-arg-list-after-template-kw]  
 1241 |         return inner_product_k_t<Size - 1>::template apply(  
      |                                                      ^  
./include/boost/gil/algorithm.hpp:1288:54: error: a template argument list is expected after a name prefixed by the template keyword [-Wmissing-template-arg-list-after-template-kw]  
 1288 |     return detail::inner_product_k_t<Size>::template apply(  
      |                                                      ^  
2 errors generated.  
```  
  
Thank you!  
*This is an amazing library and I will star it :)*

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2025-06-16 10:34:47 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/766#pullrequestreview-2931561869  

LGTM but could you @sdebionne offer your pair of eyes too?

---
