# #415 - Constructor Template Auto Deduction guides [Open]

> Username: rmpowell77  
> Created at: 2018-07-21 00:17:09 UTC  
> Updated at: 2018-07-21 00:17:09 UTC  
> Url: https://github.com/boostorg/hana/issues/415  

It seems with Hana in Boost 1.67 it doesn't have Compiler Template Auto Deduction guides.  
  
This doesn't compile:  
```c++  
#include <boost/hana.hpp>  
  
namespace hana = boost::hana;  
  
int main() {  
    auto args = hana::tuple(10, 0.5, "hello world");  
}  
```  
  
But if we add some deduction guides, it can compile:  
  
```c++  
#include <boost/hana.hpp>  
  
namespace hana = boost::hana;  
  
namespace boost::hana {  
    template <typename... Args>  
    tuple(Args&&...) -> tuple<Args...>;  
}  
  
int main() {  
    auto args = hana::tuple(10, 0.5, "hello world");  
}  
```  
  
Would be a good addition.
