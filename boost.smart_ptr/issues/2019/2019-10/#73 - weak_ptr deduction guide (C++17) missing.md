# #73 - weak_ptr deduction guide (C++17) missing [Closed]

> Username: dalle  
> Created at: 2019-10-31 16:36:03 UTC  
> Updated at: 2020-04-02 21:04:33 UTC  
> Closed at: 2020-04-02 21:04:33 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/73  

The deduction guide for `boost::weak_ptr` is missing, making code more explicit than it have to using C++17 capabilities, Class Template Argument Deduction (CTAD) in mind. Using the `std` counterpart offer this capability.  
  
See https://en.cppreference.com/w/cpp/memory/weak_ptr/deduction_guides for more information about deduction guide for `std::weak_ptr`.  
  
```  
#include <boost/smart_ptr/make_shared.hpp>  
int main()  
{  
    auto p = boost::make_shared<int>(42);  
    boost::weak_ptr<int> w1{p};  // compiles using any C++ toolset  
    boost::weak_ptr w2{p}; // expected to compile using C++17 toolset  
}  
```  
  
The actual deduction guide is trivial:  
  
```  
namespace boost  
{  
    template<class T>  
    weak_ptr(shared_ptr<T>) -> weak_ptr<T>;  
}  
```  
  
One thing I don't know is to feature test for running in C++17 toolset in the proper Boost way, I glanced at Boost.Container how it's handled there.  
https://github.com/boostorg/container/blob/develop/include/boost/container/detail/workaround.hpp#L123
