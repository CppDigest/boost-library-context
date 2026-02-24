# #250 - Custom container leads to "invalid use of incomplete type" for "struct boost::container::dtl::container_rebind" [Closed]

> Username: adambadura  
> Created at: 2023-06-19 09:44:19 UTC  
> Updated at: 2025-08-25 22:36:18 UTC  
> Closed at: 2025-05-21 10:05:44 UTC  
> Url: https://github.com/boostorg/container/issues/250  

In our project we have custom container (let's call it `CustomContainer`) that doesn't match `boost::container::dtl::container_rebind` specializations in the `include/boost/container/detail/container_rebind.hpp` file.  
  
Such a container is used in `flat_map` the following way:  
```c++  
template<typename T, std::size_t size>  
class CustomContainer;  
  
constexpr std::uint8_t size{...};  
using FlatMapType = boost::container::flat_map<  
    KeyType,  
    MappedType,  
    std::less<>,  
    CustomContainer<std::pair<KeyType, MappedType>, size>>;  
```  
  
And with GCC 10.2 and Boost 1.79 we are getting compilation errors:  
```plain  
{prefix}/usr/include/boost/container/detail/container_or_allocator_rebind.hpp:30:8: error: invalid use of incomplete type 'struct boost::container::dtl::container_rebind<CustomContainer<std::pair<KeyType, MappedType>, {size}>>, boost::container::dtl::pair<KeyType, MappedType> >'  
   30 | struct container_or_allocator_rebind_impl  
      |        ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
I have reproduced the same on Compiler Explorer: https://godbolt.org/z/eGTsYer6r. It seems to apply still in Boost 1.82 as well.  
  
My guess would be we need to provide `container_rebind` specialization for our container. It does seem to work.  
  
However, `container_rebind` is not documented as an extension point. Furthermore, it is introduced by a header in `detail/` subdirectory, making it even more uneasy to tweak with it in the user code.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-05-21 10:02:34 UTC  
> Url: https://github.com/boostorg/container/issues/250#issuecomment-2897371505  

Container rebind usage was partially removed for recent compilers, but I'll remove all usage in order to fix this bug.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2025-08-25 22:36:18 UTC  
> Url: https://github.com/boostorg/container/issues/250#issuecomment-3221947665  

Thanks for the report!
