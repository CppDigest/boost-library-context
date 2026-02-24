# #192 - callable_traits does not appear to work with c++20 std::bind_front [Closed]

> Username: edtanous  
> Created at: 2022-03-25 01:04:43 UTC  
> Updated at: 2022-10-12 01:51:54 UTC  
> Closed at: 2022-10-12 01:51:54 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/192  

A basic example is below.  Both tests for return_type and args produces.  
  
```  
error: no type named ‘type’ in ‘struct boost::callable_traits::error::return_type<boost::callable_traits::unable_to_determine_return_type_>::_  
```  
  
It's not clear if this is intentional on the part of the library, or if there's some missing functionality given that bind_front is relatively new.  Happy to look into this further, just would like some input on if there's already work in this area or this is explicitly out of scope for some reason.  
  
```c++  
#include <functional>  
#include <iostream>  
#include <boost/callable_traits.hpp>  
  
int my_function(int val){  
    return 42;  
}  
  
int main(){  
  namespace ct = boost::callable_traits;  
  
  int int_val = 0;  
  auto val = std::bind_front(my_function, int_val);  
  
  using bind_type = decltype(val);  
  
  static_assert(std::is_same_v<ct::return_type<bind_type>, int>);  
  
  using args = ct::args_t<bind_type>;  
  constexpr size_t args = std::tuple_size_v<args>;  
  static_assert(args == 0);  
  
  // The bind_front instance can produce an int  
  int out_val = val();  
  std::cout << out_val;  
  return 0;  
}  
```

---

## Comment 1

> Username: badair  
> Created at: 2022-10-12 01:51:54 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/192#issuecomment-1275481686  

Thank you for sharing. `std::bind_front` returns an object whose `operator()` is a template. It is not possible to inspect function templates without instantiating them, and there is no robust way for a library to instantiate arbitrary templates generically for this purpose. This is the reason you see this error. Unfortunately, it's also not really feasible to improve this error message either.
