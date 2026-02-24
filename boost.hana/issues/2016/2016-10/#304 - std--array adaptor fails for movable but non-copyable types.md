# #304 - std::array adaptor fails for movable but non-copyable types [Closed]

> Username: iolojz  
> Created at: 2016-10-19 09:44:37 UTC  
> Updated at: 2016-10-23 20:31:52 UTC  
> Closed at: 2016-10-23 20:31:47 UTC  
> Url: https://github.com/boostorg/hana/issues/304  

Attempting to turn a rvalue of type `std::array<Foo, N>` into a `boost::hana::tuple<...>` when `Foo` is non-copyable but movable does not work.  e.g.  
  
```  
#include <boost/hana.hpp>  
#include <boost/hana/ext/std/array.hpp>  
  
struct Foo  
{  
    Foo( void ) = default;  
    Foo( const Foo & ) = delete;  
    Foo( Foo && ) = default;  
};  
  
using bar = decltype(boost::hana::to_tuple( std::array<Foo, 42>() ));  
  
int main( void )  
{  
    return 0;  
}  
```  
  
does not compile.

---

## Comment 1

> Username: ricejasonf  
> Created at: 2016-10-22 20:12:42 UTC  
> Url: https://github.com/boostorg/hana/issues/304#issuecomment-255551086  

Hana is using `operator[]` to get the elements from the array which apparently doesn't not support rvalues. Using `std::get<n>` fixes it. I'll submit a PR momentarily.

---

## Comment 2

> Username: ldionne  
> Created at: 2016-10-23 20:31:47 UTC  
> Url: https://github.com/boostorg/hana/issues/304#issuecomment-255612365  

Fixed by #305.
