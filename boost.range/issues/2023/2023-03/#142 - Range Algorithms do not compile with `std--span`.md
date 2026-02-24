# #142 - Range Algorithms do not compile with `std::span` [Open]

> Username: gcerretani  
> Created at: 2023-03-20 13:49:02 UTC  
> Updated at: 2023-03-20 14:55:55 UTC  
> Url: https://github.com/boostorg/range/issues/142  

Algorithms like `find_if` do not compile when used on instances of `std::span`. They work with `boost::span`.  
  
You can try this code on https://godbolt.org/z/Mq4GhqMYn:  
  
```  
#include <ranges>  
#include <span>  
  
#include <boost/core/span.hpp>  
#include <boost/range/algorithm/find.hpp>  
  
constexpr int a[] { 0, 1, 2, 3, 4, 5, 6, 7, 8 };  
boost::span boost_span{a};  
std::span std_span{a};  
  
auto foo0() {  
    return boost::range::find(boost_span, 2); // ok  
}  
  
auto foo1() {  
    return boost::range::find(std_span, 2); // fails  
}  
  
auto foo2() {  
    return std::ranges::find(boost_span, 2); // ok  
}  
  
auto foo3() {  
    return std::ranges::find(std_span, 2); // ok  
}  
```  
  
The error message is something like  
  
`error: no type named 'type' in 'struct boost::range_iterator<const std::span<const int, 9>, void>'`

---

## Comment 1

> Username: gcerretani  
> Created at: 2023-03-20 14:55:55 UTC  
> Url: https://github.com/boostorg/range/issues/142#issuecomment-1476383434  

The problem depends by `std::span` that does not provice a `std::span<T>::const_iterator`, that is usually provided by the other standard containers.  
  
Could be related to https://stackoverflow.com/questions/62757700/why-does-stdspan-lack-cbegin-and-cend-methods
