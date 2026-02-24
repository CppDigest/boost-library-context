# #63 - Use of SIZE_MAX without including <cstdint> [Closed]

> Username: francoisk  
> Created at: 2023-09-04 10:00:02 UTC  
> Updated at: 2023-09-06 02:04:43 UTC  
> Closed at: 2023-09-06 02:04:43 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/63  

`sequence_container_interface.hpp` uses `SIZE_MAX` without including `<cstdint>`. This results in the following compilation failure under gcc 13 on Linux:  
  
```  
sequence_container_interface.hpp: In function ‘std::size_t boost::stl_interfaces::detail::fake_capacity(const Container&)’:  
sequence_container_interface.hpp:65:16: error: ‘SIZE_MAX’ was not declared in this scope  
   65 |         return SIZE_MAX;  
      |                ^~~~~~~~  
sequence_container_interface.hpp:15:1: note: ‘SIZE_MAX’ is defined in header ‘<cstdint>’; did you forget to ‘#include <cstdint>’?  
  
```

---

## Comment 1

> Username: tzlaine  
> Created at: 2023-09-06 02:04:34 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/63#issuecomment-1707539640  

Thanks for pointing this out!  This is fixed now, and will land in the next Boost release.
