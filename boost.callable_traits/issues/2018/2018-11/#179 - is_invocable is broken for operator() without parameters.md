# #179 - is_invocable is broken for operator() without parameters [Closed]

> Username: PeterFeicht  
> Created at: 2018-11-12 12:52:55 UTC  
> Updated at: 2018-12-18 00:22:08 UTC  
> Closed at: 2018-12-18 00:22:08 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/179  

`is_invocable` fails to recognize lambdas without parameters as well as ordinary function objects with an `operator()` without parameters.  [Live demo](https://godbolt.org/z/PUvh3O) is on Godbolt.  
  
Example, the first two assertions fail, the `std` version succeeds:  
  
```cpp  
#include <type_traits>  
#include <boost/callable_traits/is_invocable.hpp>  
  
struct Foo {  
    void operator()() {  
    }  
};  
  
int main() {  
    auto f = []() { };  
  
    static_assert(boost::callable_traits::is_invocable<Foo>::value);  
    static_assert(boost::callable_traits::is_invocable<decltype(f)>::value);  
    static_assert(std::is_invocable<Foo>::value);  
    static_assert(std::is_invocable<decltype(f)>::value);  
}  
```

---

## Comment 1

> Username: badair  
> Created at: 2018-12-17 17:39:20 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/179#issuecomment-447932068  

Thanks Peter, I will update when this is fixed.

---

## Comment 2

> Username: badair  
> Created at: 2018-12-18 00:22:08 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/179#issuecomment-448049531  

Fixed via 2f2497b. Should make it into Boost 1.70.
