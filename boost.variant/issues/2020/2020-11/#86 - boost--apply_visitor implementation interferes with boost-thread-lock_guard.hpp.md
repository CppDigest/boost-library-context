# #86 - boost::apply_visitor implementation interferes with boost/thread/lock_guard.hpp [Closed]

> Username: Nekrolm  
> Created at: 2020-11-19 17:27:09 UTC  
> Updated at: 2020-11-19 17:36:21 UTC  
> Closed at: 2020-11-19 17:36:21 UTC  
> Url: https://github.com/boostorg/variant/issues/86  

Hi!  
  
The `boost::apply_visitor` for rvalue visitables triggers CE if appears in same TU with `boost/thread/lock_guard.hpp`. For some compilers CE depends on inclusion order:  
  
```C++  
#include <boost/thread/lock_guard.hpp> // comment to fix CE  
#include <boost/variant.hpp>  
   
using IntOrFloat = boost::variant<int, float>;  
  
int main() {  
    IntOrFloat v = 5;  
    auto vis = [](auto&& x) {};  
    boost::apply_visitor(vis, std::move(v));  
}  
```  
  
live example (gcc, clang, msvc): https://godbolt.org/z/c575hW  
  
It looks like  `boost/thread/lock_guard.hpp` injects an invalid specialization for `boost::move` which messes things up.
