# #129 - Only inherit when a class is not polymorphic [Closed]

> Username: pfultz2  
> Created at: 2016-03-07 04:35:34 UTC  
> Updated at: 2016-03-23 18:40:16 UTC  
> Closed at: 2016-03-22 23:54:13 UTC  
> Url: https://github.com/boostorg/hof/issues/129  

``` cpp  
#include <boost/fit/flip.hpp>  
  
struct base { base(int) {} };  
  
struct derived : virtual base {  
    derived() : base(1) {}  
    void operator()(int, void *) const {}  
};  
  
int main() {  
    boost::fit::flip(derived())(nullptr, 2);  
}   
```
