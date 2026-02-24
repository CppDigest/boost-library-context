# #147 - BOOST_DELETED_FUNCTION(...); produces 1683 "extra ‘;’ [-Wpedantic]" warnings. [Closed]

> Username: evoskuil  
> Created at: 2022-06-01 00:01:18 UTC  
> Updated at: 2022-06-01 01:43:57 UTC  
> Closed at: 2022-06-01 01:43:11 UTC  
> Url: https://github.com/boostorg/iostreams/issues/147  

All warnings are from iostreams. From the macro implementation, BOOST_DELETED_FUNCTION should not be terminated.  
```cpp  
#if !defined(BOOST_NO_CXX11_DELETED_FUNCTIONS)  
#   define BOOST_DELETED_FUNCTION(fun) fun = delete;  
#else  
#   define BOOST_DELETED_FUNCTION(fun) private: fun;  
#endif  
```  
https://github.com/boostorg/config/blob/develop/include/boost/config/detail/suffix.hpp#L742-L746

---

## Comment 1

> Username: mclow  
> Created at: 2022-06-01 00:14:46 UTC  
> Url: https://github.com/boostorg/iostreams/issues/147#issuecomment-1142827834  

Searching for `BOOST_DELETED_FUNCTION.+;$`, I found:  
  
```  
iostreams/detail/functional.hpp:41:     BOOST_DELETED_FUNCTION(device_close_operation& operator=(const device_close_operation&));  
iostreams/detail/functional.hpp:55:     BOOST_DELETED_FUNCTION(filter_close_operation& operator=(const filter_close_operation&));  
iostreams/detail/functional.hpp:81:     BOOST_DELETED_FUNCTION(device_close_all_operation& operator=(const device_close_all_operation&));  
iostreams/detail/functional.hpp:92:     BOOST_DELETED_FUNCTION(filter_close_all_operation& operator=(const filter_close_all_operation&));  
iostreams/detail/functional.hpp:118:     BOOST_DELETED_FUNCTION(member_close_operation& operator=(const member_close_operation&));  
iostreams/detail/functional.hpp:136:     BOOST_DELETED_FUNCTION(reset_operation& operator=(const reset_operation&));  
iostreams/detail/functional.hpp:152:     BOOST_DELETED_FUNCTION(clear_flags_operation& operator=(const clear_flags_operation&));  
iostreams/detail/functional.hpp:176:     BOOST_DELETED_FUNCTION(flush_buffer_operation& operator=(const flush_buffer_operation&));  
```  
  
If you remove those trailing semicolons, do all the warnings from iostreams go away?

---

## Comment 2

> Username: mclow  
> Created at: 2022-06-01 00:15:25 UTC  
> Url: https://github.com/boostorg/iostreams/issues/147#issuecomment-1142829435  

One more:  
```  
iostreams/detail/adapter/concept_adapter.hpp:121:     BOOST_DELETED_FUNCTION(concept_adapter& operator=(const concept_adapter&));  
```

---

## Comment 3

> Username: evoskuil  
> Created at: 2022-06-01 01:36:41 UTC  
> Url: https://github.com/boostorg/iostreams/issues/147#issuecomment-1143012987  

Yes, 187 each - for a total of 1683.

---

## Comment 4

> Username: evoskuil  
> Created at: 2022-06-01 01:43:57 UTC  
> Url: https://github.com/boostorg/iostreams/issues/147#issuecomment-1143025871  

Thanks @mclow !
