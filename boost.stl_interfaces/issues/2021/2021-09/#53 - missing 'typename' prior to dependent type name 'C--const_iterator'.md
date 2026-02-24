# #53 - missing 'typename' prior to dependent type name 'C::const_iterator' [Closed]

> Username: francoisk  
> Created at: 2021-09-06 09:35:12 UTC  
> Updated at: 2021-12-09 01:16:28 UTC  
> Closed at: 2021-12-09 01:16:28 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/53  

This is an error under Clang on Linux but only a warning under Clang on Windows. In both cases it's Clang 11 and 12.  
  
```  
include/boost/stl_interfaces/sequence_container_interface.hpp:915:30:   
error: missing 'typename' prior to dependent type name 'C::const_iterator'  
        constexpr void erase(C::const_iterator position)  
                             ^~~~~~~~~~~~~~~~~  
                             typename   
```

---

## Comment 1

> Username: tzlaine  
> Created at: 2021-12-09 01:16:03 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/53#issuecomment-989373411  

Thanks for reporting.  Sorry for the delay.
