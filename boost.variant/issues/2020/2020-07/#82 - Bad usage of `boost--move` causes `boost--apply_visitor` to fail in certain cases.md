# #82 - Bad usage of `boost::move` causes `boost::apply_visitor` to fail in certain cases [Closed]

> Username: akrzemi1  
> Created at: 2020-07-20 10:28:46 UTC  
> Updated at: 2020-07-23 12:30:21 UTC  
> Closed at: 2020-07-23 12:30:21 UTC  
> Url: https://github.com/boostorg/variant/issues/82  

The following program fails to compile:  
  
```c++  
#include <boost/thread/locks.hpp> // REMOVE this include and program will compile  
#include <boost/variant.hpp>  
  
boost::variant<int> make_variant() {  
    return 1;  
}  
  
int main() {  
    auto visitor = [](auto&&) {};  
    boost::apply_visitor(visitor, make_variant());  
}  
```  
  
Here is a Wandbox link:  
https://wandbox.org/permlink/p55KKRYU3jqjZbIr  
  
Removing the `#include` of Boost.Thread helps because the latter provides an overload of `boost::move()`; however, the problem is caused by the use of `boost::move()` in `<boost/variant/variant.hpp>` at https://github.com/boostorg/variant/blob/master/include/boost/variant/variant.hpp#L1021  
  
Function `internal_visit` provides template parameters to `boost::move()` explicitly, which disables SFINAE and causes the overload from Boost.Thread to fail.

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-07-23 12:30:21 UTC  
> Url: https://github.com/boostorg/variant/issues/82#issuecomment-662979097  

Many many many thanks for the description of the fix! I've been scratching my head since #53 and could not locate the bad usage
