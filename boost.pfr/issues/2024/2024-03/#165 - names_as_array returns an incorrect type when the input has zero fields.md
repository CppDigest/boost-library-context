# #165 - names_as_array returns an incorrect type when the input has zero fields [Closed]

> Username: anarthal  
> Created at: 2024-03-21 18:40:27 UTC  
> Updated at: 2025-06-04 16:06:44 UTC  
> Closed at: 2025-06-04 16:06:42 UTC  
> Url: https://github.com/boostorg/pfr/issues/165  

Compiler: gcc13.1, Linux  
Boost 1.84  
Godbolt: https://godbolt.org/z/xxT1Tsoe7  
Description: when `names_as_array` is called on a struct with no fields, `std::array<std::nullptr_t, N>` is returned, rather than `std::array<std::string_view, 0>`. This makes generic code more difficult.  
  
```  
#include <boost/pfr.hpp>  
  
namespace pfr = boost::pfr;  
  
struct MyType {};  
  
int main()  
{  
    std::array<std::string_view, 0> names = pfr::names_as_array<MyType>();   
}  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2024-03-21 18:42:23 UTC  
> Url: https://github.com/boostorg/pfr/issues/165#issuecomment-2013281683  

Happens on clang-19 (on my machine) too.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-06-04 16:06:42 UTC  
> Url: https://github.com/boostorg/pfr/issues/165#issuecomment-2940598603  

This seems to have been fixed in one of the recent commits.
