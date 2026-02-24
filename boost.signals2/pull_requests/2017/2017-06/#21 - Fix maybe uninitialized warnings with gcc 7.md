# #21 Fix maybe uninitialized warnings with gcc 7. [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2017-06-21 21:12:36 UTC  
> Updated at: 2017-06-22 17:37:59 UTC  
> Merged at: 2017-06-22 17:37:59 UTC  
> Closed at: 2017-06-22 17:37:59 UTC  
> Url: https://github.com/boostorg/signals2/pull/21  

Hi,  
  
I am having new warnings when migration to gcc 7. With -Wall -Wextra -Werror I have this when using the library very basically:  
  
```  
/remote/mw/combld/delivery/unofficial.17/components.osp/osp/Boost/17-0-0-0/include/boost/signals2/detail/auto_buffer.hpp:307:27: error: ‘local_lock.boost::signals2::detail::auto_buf  
fer<boost::shared_ptr<void>, boost::signals2::detail::store_n_objects<10> >::size_’ may be used uninitialized in this function [-Werror=maybe-uninitialized]  
             BOOST_ASSERT( size_ <= members_.capacity_ );  
                           ^~~~~  
```  
  
when looking a bit to the code, this seems linked to the fact that you often call the destructor of "this" and continue to use it afterwards, which apparently gcc doesn't like despite that all the auto_buffer members are normally trivially destructible (while auto_buffer itself might not be if storing a non trivially destructible type). Moving the destruction into it's own method rather than the destructor helps gcc to understand everything correctly.  
  
Cheers,  
Romain

---
