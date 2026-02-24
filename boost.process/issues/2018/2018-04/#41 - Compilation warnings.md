# #41 - Compilation warnings [Closed]

> Username: k15tfu  
> Created at: 2018-04-26 16:01:47 UTC  
> Updated at: 2018-06-18 01:51:43 UTC  
> Closed at: 2018-06-18 01:51:43 UTC  
> Url: https://github.com/boostorg/process/issues/41  

I faced with the following warnings when compiling with clang-6.0 -Wall -Wextra:  
  
1.   
```  
include/boost/process/detail/posix/sigchld_service.hpp:98:31: warning:  
      lambda capture 'ec' is not used [-Wunused-lambda-capture]  
                _strand.post([ec]{});  
```  
  
2.   
```  
boost/process/detail/posix/async_pipe.hpp:235:54: warning: unused  
      parameter 'p' [-Wunused-parameter]  
async_pipe& async_pipe::operator=(const async_pipe & p)  
```  
  
3.   
```  
boost/process/detail/posix/async_in.hpp:63:56: warning: unused  
      parameter 'ec' [-Wunused-parameter]  
                [pipe](const boost::system::error_code&ec, std::size_t size){});  
```  
  
4.   
```  
include/boost/process/detail/posix/async_in.hpp:63:72: warning: unused  
      parameter 'size' [-Wunused-parameter]  
                [pipe](const boost::system::error_code&ec, std::size_t size){});  
```  
  
5.   
```  
include/boost/process/detail/posix/async_in.hpp:42:38: warning: unused  
      parameter 'exec' [-Wunused-parameter]  
    inline void on_success(Executor &exec)  
```  
  
6.   
```  
include/boost/process/detail/posix/async_out.hpp:64:70: warning: unused  
      parameter 'size' [-Wunused-parameter]  
                [pipe](const boost::system::error_code&, std::size_t size){});  
```  
  
7.   
```  
include/boost/process/detail/posix/async_out.hpp:60:38: warning: unused  
      parameter 'exec' [-Wunused-parameter]  
    inline void on_success(Executor &exec)  
```  
  
8.   
```  
include/boost/process/detail/posix/async_out.hpp:121:90: warning: unused  
      parameter 'size' [-Wunused-parameter]  
                [pipe, buffer, promise](const boost::system::error_code& ec, std::size_t size)  
```  
  
9.   
```  
include/boost/process/detail/posix/async_out.hpp:113:38: warning: unused  
      parameter 'exec' [-Wunused-parameter]  
    inline void on_success(Executor &exec)  
```  
  
10.  
```  
include/boost/process/async_system.hpp:85:27: warning: unused parameter  
      'exit_code' [-Wunused-parameter]  
            return [](int exit_code, const std::error_code & ec){};  
```  
  
11.  
```  
include/boost/process/async_system.hpp:85:27: warning: unused parameter  
      'ec' [-Wunused-parameter]  
            return [](int exit_code, const std::error_code & ec){};  
```  
  
12.  
```  
include/boost/process/detail/posix/group_handle.hpp:62:47: warning: unused  
      parameter 'ec' [-Wunused-parameter]  
    bool has(handle_t proc, std::error_code & ec) noexcept  
```
