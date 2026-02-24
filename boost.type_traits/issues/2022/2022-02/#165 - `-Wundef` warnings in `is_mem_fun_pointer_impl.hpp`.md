# #165 - `-Wundef` warnings in `is_mem_fun_pointer_impl.hpp` [Closed]

> Username: pdimov  
> Created at: 2022-02-10 01:40:26 UTC  
> Updated at: 2022-11-21 18:07:10 UTC  
> Closed at: 2022-11-21 18:07:10 UTC  
> Url: https://github.com/boostorg/type_traits/issues/165  

```  
..\../boost/type_traits/detail/is_mem_fun_pointer_impl.hpp:1082:5: error: "__cpp_noexcept_function_type" is not defined, evaluates to 0 [-Werror=undef]  
 1082 | #if __cpp_noexcept_function_type  
      |     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
This prevents clean `-Wundef -Werror` compilation in e.g. the Unordered test suite.

---

## Comment 1

> Username: pdimov  
> Created at: 2022-10-30 00:14:18 UTC  
> Url: https://github.com/boostorg/type_traits/issues/165#issuecomment-1296032427  

This seems to have been fixed by #172.
