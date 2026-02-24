# #11 - `fields_count` seems to not work correctly in C++17 with non-default-constructible types. [Closed]

> Username: mpark  
> Created at: 2017-09-11 21:47:48 UTC  
> Updated at: 2017-09-30 07:13:02 UTC  
> Closed at: 2017-09-30 07:13:02 UTC  
> Url: https://github.com/boostorg/pfr/issues/11  

A following test case prints `0`, rather than `4` in C++17 mode.  
  
```c++  
#include <iostream>  
#include <boost/pfr.hpp>  
  
struct X { X(int) {} };  
struct S { X x0; X x1; int x2; X x3; };  
  
int main() {  
  std::cout << boost::pfr::detail::fields_count<S>() << '\n';  
}  
```  
  
The [`detect_fields_count`](https://github.com/apolukhin/magic_get/blob/develop/include/boost/pfr/detail/fields_count.hpp#L97-L101) case for when initialization fails looks incorrect to me. Specifically, when initialization fails with `N` arguments, we don't actually know *why* it failed. It could have failed because (1) too many arguments, or (2) too few arguments. Currently the code assumes that initialization fails due to too many arguments, but as far as I understand default-constructibility is only a requirement in C++14 mode. As such, I believe the code should actually recurse in both directions.

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-09-27 17:31:10 UTC  
> Url: https://github.com/boostorg/pfr/issues/11#issuecomment-332596613  

Very good catch!  
  
I'll spend some time for implementing greedy search for cases when one of the member types is not default constructible.

---

## Comment 2

> Username: mpark  
> Created at: 2017-09-27 21:51:01 UTC  
> Updated at: 2017-09-27 21:54:57 UTC  
> Url: https://github.com/boostorg/pfr/issues/11#issuecomment-332666148  

Here's [my implementation](https://github.com/mpark/patterns/blob/master/include/mpark/patterns/detail/as_tuple.hpp#L46-L68) of it if you're interested

---

## Comment 3

> Username: apolukhin  
> Created at: 2017-09-28 18:33:10 UTC  
> Url: https://github.com/boostorg/pfr/issues/11#issuecomment-332925104  

Great thanks for reporting the issue and for helping to resolve it!
