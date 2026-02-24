# #45 - 'This header is deprecated' warnings from signal.hpp in boost 1.74 [Closed]

> Username: thebrandre  
> Created at: 2020-09-05 13:46:43 UTC  
> Updated at: 2020-09-05 15:46:59 UTC  
> Closed at: 2020-09-05 15:46:59 UTC  
> Url: https://github.com/boostorg/signals2/issues/45  

`#include <boost/signals2/signal.hpp>` in boost 1.74 generates the following warning/message:   
  
```  
In file included from /celibs/boost_1_74_0/boost/smart_ptr/detail/sp_thread_sleep.hpp:22,  
                 from /celibs/boost_1_74_0/boost/smart_ptr/detail/yield_k.hpp:23,  
                 from /celibs/boost_1_74_0/boost/smart_ptr/detail/spinlock_gcc_atomic.hpp:14,  
                 from /celibs/boost_1_74_0/boost/smart_ptr/detail/spinlock.hpp:42,  
                 from /celibs/boost_1_74_0/boost/smart_ptr/detail/spinlock_pool.hpp:25,  
                 from /celibs/boost_1_74_0/boost/smart_ptr/shared_ptr.hpp:29,  
                 from /celibs/boost_1_74_0/boost/shared_ptr.hpp:17,  
                 from /celibs/boost_1_74_0/boost/signals2/signal.hpp:21,  
                 from <source>:1:  
  
/celibs/boost_1_74_0/boost/function_output_iterator.hpp:14:1: note: '#pragma message: This header is deprecated. Use <boost/iterator/function_output_iterator.hpp> instead.'  
  
   14 | BOOST_HEADER_DEPRECATED("<boost/iterator/function_output_iterator.hpp>")  
  
      | ^~~~~~~~~~~~~~~~~~~~~~~  
```  
A demo is [here on godbolt](https://godbolt.org/z/dGhxsM).  
Note that even though the warning is generated inside `shared_ptr.hpp`, it is not emitted by the compiler if you include only `boost/shared_ptr.hpp` (as demonstrated in the link to compiler explorer).

---

## Comment 1

> Username: fmhess  
> Created at: 2020-09-05 15:46:59 UTC  
> Url: https://github.com/boostorg/signals2/issues/45#issuecomment-687627854  

This was fixed in commit  
  
fd27423fea5537bc857c1fa14bb0c25b994f77b3  
  
however, it was not merged into master until after the 1.74 release.
