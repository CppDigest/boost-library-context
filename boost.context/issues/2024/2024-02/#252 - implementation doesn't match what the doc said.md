# #252 - implementation doesn't match what the doc said [Closed]

> Username: zhaohui0923  
> Created at: 2024-02-20 01:18:24 UTC  
> Updated at: 2024-03-10 19:21:46 UTC  
> Closed at: 2024-03-10 19:21:46 UTC  
> Url: https://github.com/boostorg/context/issues/252  

``` cpp  
// boost\libs\context\src\windows\stack_traits.cpp  
  
BOOST_CONTEXT_DECL  
std::size_t  
stack_traits::default_size() BOOST_NOEXCEPT_OR_NOTHROW {  
    return 128 * 1024;  
}  
  
BOOST_CONTEXT_DECL  
bool  
stack_traits::is_unbounded() BOOST_NOEXCEPT_OR_NOTHROW {  
    return true;  
}  
```  
for stack_traits::default_size() the doc says  
> Returns a default stack size, which may be platform specific. If the stack is unbounded then the present implementation returns the maximum of 64 kB and minimum_size().  
  
The implementation code and the doc are not matched.

---

## Comment 1

> Username: olk  
> Created at: 2024-03-10 19:21:46 UTC  
> Url: https://github.com/boostorg/context/issues/252#issuecomment-1987337208  

done - ty
