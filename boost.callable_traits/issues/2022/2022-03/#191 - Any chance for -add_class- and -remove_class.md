# #191 - Any chance for "add_class" and "remove_class"? [Closed]

> Username: OptimumCpp  
> Created at: 2022-03-10 15:47:44 UTC  
> Updated at: 2022-10-12 01:34:45 UTC  
> Closed at: 2022-10-12 01:34:45 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/191  

Hi,  
Thank you for the huge job of considering every corner case. You have already added tools for trailing arguments (varargs). Is it possible to add/remove a "this" argument type ("Class::*" ) to/from the beginning of a free/member function?  
  
Regards,  
FM.

---

## Comment 1

> Username: badair  
> Created at: 2022-10-12 01:34:04 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/191#issuecomment-1275470847  

Hi, sorry for the late response. Yes, it is possible:  
```  
#include <type_traits>  
#include <boost/callable_traits.hpp>  
#include <boost/mp11/list.hpp>  
  
using T = void(int);  
class C {};  
using target = void(C::*)(int);  
  
// two ways to add member pointer:  
static_assert( std::is_same_v<T C::*, target> );  
static_assert( std::is_same_v<boost::callable_traits::apply_member_pointer_t<T, C>, target> );  
  
// remove member pointer:  
template<typename T>  
using remove_member_pointer_t =  
    boost::callable_traits::apply_return_t<  
        boost::mp11::mp_pop_front<boost::callable_traits::args_t<target>>,  
        boost::callable_traits::return_type_t<target>>;  
  
static_assert( std::is_same_v<T, remove_member_pointer_t<target>>);  
```
