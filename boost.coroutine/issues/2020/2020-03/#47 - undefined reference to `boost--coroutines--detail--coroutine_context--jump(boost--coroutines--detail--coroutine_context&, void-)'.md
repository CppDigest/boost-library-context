# #47 - undefined reference to `boost::coroutines::detail::coroutine_context::jump(boost::coroutines::detail::coroutine_context&, void*)' [Closed]

> Username: lxffff1990  
> Created at: 2020-03-17 09:09:58 UTC  
> Updated at: 2020-03-17 10:44:37 UTC  
> Closed at: 2020-03-17 10:44:37 UTC  
> Url: https://github.com/boostorg/coroutine/issues/47  

**/usr/local/include/boost/coroutine/detail/push_coroutine_impl.hpp:249:** undefined reference to `boost::coroutines::detail::coroutine_context::jump(boost::coroutines::detail::coroutine_context&, void*)'  
  
1. boost version 1.72  
2. Linux ubuntu 4.15.0-91-generic #92~16.04.1-Ubuntu SMP Fri Feb 28 14:57:22 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux  
3. gcc version 5.4.0 20160609 (Ubuntu 5.4.0-6ubuntu1~16.04.12)  
-lpthread -lboost_system -lboost_filesystem -lboost_program_options -lboost_coroutine -lboost_thread -lboost_context
