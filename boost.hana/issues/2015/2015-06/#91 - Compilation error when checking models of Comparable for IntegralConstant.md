# #91 - [Core] Compilation error when checking models of Comparable for IntegralConstant [Closed]

> Username: ldionne  
> Created at: 2015-06-12 18:12:44 UTC  
> Updated at: 2015-06-12 22:50:37 UTC  
> Closed at: 2015-06-12 22:50:37 UTC  
> Url: https://github.com/boostorg/hana/issues/91  

The following code does not compile:  
  
``` c++  
#include <boost/hana/core/models.hpp>  
#include <boost/hana/integral_constant.hpp>  
using namespace boost::hana;  
  
  
using T = decltype(int_<1>);  
  
static_assert(models<Comparable, T>(), "");  
static_assert(models<Orderable, T>(), "");  
static_assert(models<Logical, T>(), "");  
static_assert(models<Monoid, T>(), "");  
static_assert(models<Group, T>(), "");  
static_assert(models<Ring, T>(), "");  
static_assert(models<IntegralDomain, T>(), "");  
  
int main() { }  
```  
  
The failure is not caused by a failing `static_assert`, but by a weird recursion inside the model checking for concepts with binary methods.
