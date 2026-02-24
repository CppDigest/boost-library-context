# #23 - Clang warnings for described internal types [Closed]

> Username: ecatmur  
> Created at: 2021-11-16 20:55:50 UTC  
> Updated at: 2021-11-17 00:50:18 UTC  
> Closed at: 2021-11-17 00:50:18 UTC  
> Url: https://github.com/boostorg/describe/issues/23  

If I describe an enum or class in an unnamed namespace, clang emits warnings, either (if the descriptor is not used)  
```  
warning: unused function 'boost_enum_descriptor_fn' [-Wunused-function]  
```  
or, if the descriptor is used:  
```  
warning: function 'boost_enum_descriptor_fn' is not needed and will not be emitted [-Wunneeded-internal-declaration]  
```  
  
Example: https://godbolt.org/z/qPrdcT5KG  
```c++  
#include <boost/describe.hpp>  
namespace {  
BOOST_DEFINE_ENUM(E, A);  
class S { BOOST_DESCRIBE_CLASS(S, (), (), (), ()); };  
}  
using namespace boost::describe;  
using D = describe_enumerators<E>;  
using B = describe_bases<S, mod_any_access>;  
using M = describe_members<S, mod_any_access>;  
```  
  
It looks like these can be suppressed with [[maybe_unused]]; I'll submit a PR.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-11-17 00:50:18 UTC  
> Url: https://github.com/boostorg/describe/issues/23#issuecomment-970972366  

Thanks.
