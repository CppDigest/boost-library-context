# #39 - make ICL_IMPL_SPACE configurable, to be able to use external sets (e.g. abseil btree_set) [Open]

> Username: ray-haeb  
> Created at: 2023-06-14 16:27:31 UTC  
> Updated at: 2023-06-14 16:31:14 UTC  
> Url: https://github.com/boostorg/icl/issues/39  

use something like:  
```  
#if defined(ICL_USE_BOOST_MOVE_IMPLEMENTATION)  
#   define ICL_IMPL_SPACE boost::container  
#elif defined(ICL_USE_STD_IMPLEMENTATION)  
#   define ICL_IMPL_SPACE std  
#elif !defined(ICL_IMPL_SPACE)  
#   define ICL_IMPL_SPACE std  
#endif  
```  
in impl_config.hpp
