# #39 - undefined behavior in do_fill_uninitialized_memory() [Open]

> Username: usefulcat  
> Created at: 2021-02-04 20:10:24 UTC  
> Updated at: 2021-02-09 05:17:21 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/39  

Using gcc 9.1 with -fsanitize=undefined and -DBOOST_CB_ENABLE_DEBUG=1, the following code results in a ubsan error: "boost/circular_buffer/debug.hpp:37:16: runtime error: null pointer passed as argument 1, which is declared to never be null".  
  
```  
using Q = boost::circular_buffer<int>;    
Q q;      
const Q q2(q);  
```  
  
The ubsan error is a result of the following code in circular_buffer/debug.h:  
  
```  
template <class T>  
inline void do_fill_uninitialized_memory(T* data, std::size_t size_in_bytes) BOOST_NOEXCEPT {  
    std::memset(static_cast<void*>(data), UNINITIALIZED, size_in_bytes);  
}  
```  
  
During copy construction, the function gets called with data == nullptr and size_in_bytes == 0. I believe that passing a null pointer to memset is technically undefined behavior even if the size is 0.  
  
Changing the above function as follows avoids the ubsan error:  
  
```  
template <class T>  
inline void do_fill_uninitialized_memory(T* data, std::size_t size_in_bytes) BOOST_NOEXCEPT {  
    if (size_in_bytes != 0u) {  
        std::memset(static_cast<void*>(data), UNINITIALIZED, size_in_bytes);  
    }  
}  
```  
  
I had originally written it to check for data != nullptr, but since this is debug code I thought it seemed desirable to know if the function is ever called with data == null and size_in_bytes != 0. In any case, either way will prevent the ubsan error.
