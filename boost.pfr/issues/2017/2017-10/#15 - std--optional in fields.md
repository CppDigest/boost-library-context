# #15 - std::optional in fields [Closed]

> Username: LawlietRyuzaki  
> Created at: 2017-10-14 13:09:23 UTC  
> Updated at: 2017-10-17 06:01:18 UTC  
> Closed at: 2017-10-16 20:55:15 UTC  
> Url: https://github.com/boostorg/pfr/issues/15  

```c++  
#include <optional>  
#include <boost/pfr.hpp>  
  
struct Foo {  
    std::optional<int> a;  
};  
  
int main() {  
    Foo f{5};  
    boost::pfr::get<0>(f);  
}  
```  
  
Is there any way to compile this code?  
  
I compiled with gcc 6.3 and gcc 7.1 and get the following errors:  
`error: conversion from 'boost::pfr::detail::loophole_ubiq<Foo, 0>' to 'std::optional<int>' is ambiguous  
     : sequence_tuple::tuple< decltype(T{ loophole_ubiq<T, I>{}... }, 0) > // Instantiating loopholes.`

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-10-17 06:01:18 UTC  
> Url: https://github.com/boostorg/pfr/issues/15#issuecomment-337126620  

Fixed the issue! Thanks for providing the test case.
