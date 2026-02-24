# #227 - [type] Pattern matching with `basic_type` is broken. [Closed]

> Username: ricejasonf  
> Created at: 2015-12-21 23:44:14 UTC  
> Updated at: 2015-12-22 03:09:49 UTC  
> Closed at: 2015-12-22 00:34:46 UTC  
> Url: https://github.com/boostorg/hana/issues/227  

Per the documentation,  
  
``` cpp  
    //! @note  
    //! For subtle reasons having to do with ADL, the actual representation of  
    //! `hana::type` is implementation-defined. In particular, `hana::type`  
    //! may be a dependent type, so one should not attempt to do pattern  
    //! matching on it. However, one can assume that `hana::type` _inherits_  
    //! from `hana::basic_type`, which can be useful when declaring overloaded  
    //! functions:  
    //! @code  
    //!     template <typename T>  
    //!     void f(hana::basic_type<T>) {  
    //!         // do something with T  
    //!     }  
    //! @endcode  
```  
  
Not only does `type_impl` not _inherit_ from `basic_tuple`, but the above technique does not work.   
Consider the following example:  
  
``` cpp  
#include<boost/hana.hpp>  
  
namespace hana = boost::hana;  
  
  
template<typename T>  
constexpr bool foo(T)  
{  
    return false;  
}  
  
template<typename T>  
constexpr bool foo(hana::basic_type<T>)  
{  
    return true;  
}  
  
constexpr auto bar = hana::type_c<int>;  
  
static_assert(foo(bar), "");  
  
int main() { }  
```

---

## Comment 1

> Username: ricejasonf  
> Created at: 2015-12-22 00:34:46 UTC  
> Url: https://github.com/boostorg/hana/issues/227#issuecomment-166466924  

Ah crap... disregard this.
