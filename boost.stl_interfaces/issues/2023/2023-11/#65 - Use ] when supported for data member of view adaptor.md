# #65 - Use [[no_unique_address]] when supported for data member of view adaptor? [Closed]

> Username: patrickroberts  
> Created at: 2023-11-22 19:03:50 UTC  
> Updated at: 2024-10-24 01:46:31 UTC  
> Closed at: 2024-10-24 01:46:31 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/65  

Maybe for compatibility it can be a conditionally defined preprocessor macro such as `BOOST_STL_INTERFACES_NO_UNIQUE_ADDRESS`?  
  
https://github.com/boostorg/stl_interfaces/blob/510eb431fbf64e98b6870468aa523dd4c6a6c69a/include/boost/stl_interfaces/view_adaptor.hpp#L312  
  
So something like:  
  
```cpp  
// <boost/stl_interfaces/config.hpp>  
  
#if defined(__has_cpp_attribute)  
#    if __has_cpp_attribute(no_unique_address)  
#        define BOOST_STL_INTERFACES_NO_UNIQUE_ADDRESS [[no_unique_address]]  
#    endif  
#endif  
  
#if !defined(BOOST_STL_INTERFACES_NO_UNIQUE_ADDRESS)  
#    define BOOST_STL_INTERFACES_NO_UNIQUE_ADDRESS  
#endif  
```  
  
and then change the line to  
  
```cpp  
BOOST_STL_INTERFACES_NO_UNIQUE_ADDRESS F f_;  
```  
  
The advantage of this is that if `std::is_empty_v<F>` then `std::is_empty_v<boost::stl_interfaces::adaptor<F>>` will also be true, which allows it in turn to be used as a potentially empty data member in another class and not occupy additional address space in the layout.

---

## Comment 1

> Username: patrickroberts  
> Created at: 2023-11-22 19:23:45 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/65#issuecomment-1823379016  

If there is a desire to still get EBO when [[no_unique_address]] is not supported, then `adaptor<F>` can inherit from `F` when `std::is_empty_v<F>` and also `!std::is_final_v<F>`, otherwise EBO isn't possible. But that would require a partial class template specialization, which would be a bit more involved change than the suggestion above.

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-10-24 01:46:18 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/65#issuecomment-2434053778  

This is a good idea.  Sorry it took me so long to get to it.
