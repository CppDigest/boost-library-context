# #51 - Boost failed when build with MSVC ON windows [Closed]

> Username: shanshan0309  
> Created at: 2018-05-10 08:15:40 UTC  
> Updated at: 2018-06-04 10:22:50 UTC  
> Closed at: 2018-06-04 10:22:50 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/51  

Boost failed with error C2664: 'boost::shared_ptr<X> boost::atomic_load_explicit<X>(const boost::shared_ptr<X> *,int) noexcept': cannot convert argument 2 from 'const boost::memory_order' to 'int'. This issue was found long ago.    
  
boost::memory_order is enum class (see boost/memory_order.hpp). So it can't be implicitly converted to 'int'.   
  
We are no longer hit this issue when I changed code 'enum class memory_order : unsigned int' as 'enum memory_order : unsigned int' in memory_order.hpp.   
  
Could you please help take a look? Thanks a lot!  
  
Error info:  
.\boost/config/compiler/visualc.hpp(348): note: Info: Boost.Config is older than your compiler version - probably nothing bad will happen - but you may wish to look for an updated Boost version. Define BOOST_CONFIG_SUPPRESS_OUTDATED_MESSAGE to suppress this message.  
libs\smart_ptr\test\atomic_sp_test.cpp(72): error C2664: 'boost::shared_ptr<T> boost::atomic_shared_ptr<T>::load(int) noexcept const': cannot convert argument 1 from 'const boost::memory_order' to 'int'  
        with  
        [  
            T=X  
        ]  
libs\smart_ptr\test\atomic_sp_test.cpp(72): note: This conversion requires an explicit cast (static_cast, C-style cast or function-style cast)  
libs\smart_ptr\test\atomic_sp_test.cpp(115): error C2664: 'void boost::atomic_shared_ptr<X>::store(boost::shared_ptr<T>,int) noexcept': cannot convert argument 2 from 'const boost::memory_order' to 'int'  
        with  
        [  
            T=X  
        ]  
libs\smart_ptr\test\atomic_sp_test.cpp(115): note: This conversion requires an explicit cast (static_cast, C-style cast or function-style cast)  
  
Thanks,  
Shanshan

---

## Comment 1

> Username: pdimov  
> Created at: 2018-05-10 10:41:43 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/51#issuecomment-388018828  

This was fixed in https://github.com/boostorg/smart_ptr/commit/4025698fe8583aa085e251e449804aeb4d6e0388

---

## Comment 2

> Username: shanshan0309  
> Created at: 2018-06-04 10:22:50 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/51#issuecomment-394306803  

Thank you Peter. I tried your fix and it works well.
