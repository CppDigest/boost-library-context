# #198 future.hpp: Fixed name shadowing [Merged]

> Username: Kojoley  
> Created at: 2017-12-24 00:30:25 UTC  
> Updated at: 2017-12-30 10:57:59 UTC  
> Merged at: 2017-12-30 09:23:46 UTC  
> Closed at: 2017-12-30 09:23:46 UTC  
> Url: https://github.com/boostorg/thread/pull/198  

```  
gcc.compile.c++ ../../../../bin.v2/libs/thread/build/gcc-gnu-6/release/cxxstd-03-iso/threadapi-pthread/threading-multi/pthread/thread.o  
In file included from ../../../../libs/thread/src/pthread/thread.cpp:19:0:  
../../../../boost/thread/future.hpp: In constructor ‘boost::detail::shared_state<T>::shared_state(const boost::exceptional_ptr&)’:  
../../../../boost/thread/future.hpp:545:52: error: declaration of ‘ex’ shadows a member of ‘boost::detail::shared_state<T>’ [-Werror=shadow]  
             shared_state(exceptional_ptr const& ex):  
                                                    ^  
In file included from ../../../../libs/thread/src/pthread/thread.cpp:19:0:  
../../../../boost/thread/future.hpp:162:31: note: shadowed declaration is here  
             executor_ptr_type ex;  
                               ^~  
In file included from ../../../../libs/thread/src/pthread/thread.cpp:19:0:  
../../../../boost/thread/future.hpp: In constructor ‘boost::detail::shared_state<T&>::shared_state(const boost::exceptional_ptr&)’:  
../../../../boost/thread/future.hpp:731:52: error: declaration of ‘ex’ shadows a member of ‘boost::detail::shared_state<T&>’ [-Werror=shadow]  
             shared_state(exceptional_ptr const& ex):  
                                                    ^  
In file included from ../../../../libs/thread/src/pthread/thread.cpp:19:0:  
../../../../boost/thread/future.hpp:162:31: note: shadowed declaration is here  
             executor_ptr_type ex;  
                               ^~  
In file included from ../../../../libs/thread/src/pthread/thread.cpp:19:0:  
../../../../boost/thread/future.hpp: In constructor ‘boost::detail::shared_state<void>::shared_state(const boost::exceptional_ptr&)’:  
../../../../boost/thread/future.hpp:810:52: error: declaration of ‘ex’ shadows a member of ‘boost::detail::shared_state<void>’ [-Werror=shadow]  
             shared_state(exceptional_ptr const& ex):  
                                                    ^  
In file included from ../../../../libs/thread/src/pthread/thread.cpp:19:0:  
../../../../boost/thread/future.hpp:162:31: note: shadowed declaration is here  
             executor_ptr_type ex;  
                               ^~  
```

---
