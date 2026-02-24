# #1 missing include <limits> [Merged]

> Username: Flast  
> Created at: 2014-05-27 02:12:02 UTC  
> Updated at: 2014-06-18 06:50:13 UTC  
> Merged at: 2014-05-27 07:50:12 UTC  
> Closed at: 2014-05-27 07:50:12 UTC  
> Url: https://github.com/boostorg/math/pull/1  

Introduced in e6996e11  
  
compile error will occur like following.  
  
```  
In file included from libs/math/config/has_long_double_support.cpp:6:0:  
./boost/math/tools/config.hpp:309:11: error: ‘numeric_limits’ is not a member of ‘std’  
       || (std::numeric_limits<T>::is_specialized && std::numeric_limits<T>::is_integer)  
           ^  
./boost/math/tools/config.hpp:309:32: error: expected primary-expression before ‘>’ token  
       || (std::numeric_limits<T>::is_specialized && std::numeric_limits<T>::is_integer)  
                                ^  
./boost/math/tools/config.hpp:309:33: error: ‘::is_specialized’ has not been declared  
       || (std::numeric_limits<T>::is_specialized && std::numeric_limits<T>::is_integer)  
                                 ^  
./boost/math/tools/config.hpp:309:53: error: ‘numeric_limits’ is not a member of ‘std’  
       || (std::numeric_limits<T>::is_specialized && std::numeric_limits<T>::is_integer)  
                                                     ^  
./boost/math/tools/config.hpp:309:74: error: expected primary-expression before ‘>’ token  
       || (std::numeric_limits<T>::is_specialized && std::numeric_limits<T>::is_integer)  
                                                                          ^  
./boost/math/tools/config.hpp:309:75: error: ‘::is_integer’ has not been declared  
       || (std::numeric_limits<T>::is_specialized && std::numeric_limits<T>::is_integer)  
```

---
