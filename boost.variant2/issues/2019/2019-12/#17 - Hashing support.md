# #17 - Hashing support [Closed]

> Username: ozars  
> Created at: 2019-12-10 20:12:14 UTC  
> Updated at: 2020-01-11 16:36:31 UTC  
> Closed at: 2020-01-11 16:36:31 UTC  
> Url: https://github.com/boostorg/variant2/issues/17  

variant2 doesn't seem to support hashing (either std::hash or boost::hash). It would be nice if it had support for either or both.  
  
For reference, std::variant [supports](https://timsong-cpp.github.io/cppwp/n4659/variant.hash) the former and boost::variant supports both.   
  
Note that AFAICS behaviors of std::hash and boost::hash aren't exactly same. `std::hash` will be disabled if any T is non-hashable, but `boost::hash` causes error while visiting for the same case (as of Boost 1.71).  
  
Thanks for this great library.

---

## Comment 1

> Username: ozars  
> Created at: 2019-12-10 20:43:21 UTC  
> Updated at: 2019-12-10 20:45:53 UTC  
> Url: https://github.com/boostorg/variant2/issues/17#issuecomment-564249386  

Here is a straight forward implementation for boost::hash in C++14:  
  
```cpp  
#include <iostream>  
  
#include <boost/functional/hash.hpp>  
#include <boost/variant2/variant.hpp>  
  
namespace boost::variant2 {  
  
template <typename... Ts>  
std::size_t hash_value(const variant<Ts...>& v) {  
    return visit(  
        [](const auto& value) -> std::size_t {  
            return boost::hash<std::decay_t<decltype(value)>>{}(value);  
        },  
        v);  
}  
  
}  // namespace boost::variant2  
  
int main() {  
  boost::variant2::variant<int, float> v {1234};  
  std::cout << boost::hash<decltype(v)>{}(v) << std::endl;  
}  
```

---

## Comment 2

> Username: pdimov  
> Created at: 2020-01-11 15:18:10 UTC  
> Url: https://github.com/boostorg/variant2/issues/17#issuecomment-573325549  

Implemented in develop, thanks for the suggestion.

---

## Comment 3

> Username: ozars  
> Created at: 2020-01-11 16:36:31 UTC  
> Url: https://github.com/boostorg/variant2/issues/17#issuecomment-573332172  

Thanks.
