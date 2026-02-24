# #466 - How to use v2 initializers [Open]

> Username: mike-5345  
> Created at: 2025-03-06 04:31:33 UTC  
> Updated at: 2025-11-18 13:43:53 UTC  
> Url: https://github.com/boostorg/process/issues/466  

Hello,  
  
I am excited to use process v2 in my projects. Unfortunately I am having a bit of a hard time understanding the API. If this is not the correct place to post such questions I apologize.  
  
Please see my following example. I cannot get the `on_*` functions to run. I am sure I am doing something wrong:  
  
```  
namespace asio = boost::asio;  
namespace bp = boost::process::v2;  
using namespace std::chrono_literals;  
  
struct CustomInit  
{  
   template <typename Launcher>  
   std::error_code on_setup(Launcher&, const std::filesystem::path&, const char * const *&)  
   {  
      std::cout << "on_setup" << std::endl;  
      return {};  
   }  
  
   template <typename Launcher>  
   std::error_code on_error(Launcher&, const std::filesystem::path&, const char * const *&)  
   {  
      std::cout << "on_error" << std::endl;  
      return {};  
   }  
  
   template <typename Launcher>  
   std::error_code on_success(Launcher&, const std::filesystem::path&, const char * const *&)  
   {  
      std::cout << "on_success" << std::endl;  
      return {};  
   }  
  
   template <typename Launcher>  
   std::error_code on_fork_error(Launcher&, const std::filesystem::path&, const char * const *&)  
   {  
      std::cout << "on_fork_error" << std::endl;  
      return {};  
   }  
  
   template <typename Launcher>  
   std::error_code on_exec_setup(Launcher&, const std::filesystem::path&, const char * const *&)  
   {  
      std::cout << "on_exec_setup" << std::endl;  
      return {};  
   }  
  
   template <typename Launcher>  
   std::error_code on_exec_error(Launcher&, const std::filesystem::path&, const char * const *&)  
   {  
      std::cout << "on_exec_error" << std::endl;  
      return {};  
   }  
};  
  
int  
main(void)  
{  
   asio::io_context io;  
     
   try {  
      auto p = bp::process(io, "/usr/bin/sleep", {"1"}, CustomInit{});  
  
      bp::async_execute(std::move(p),  
      [] (const boost::system::error_code &, const int exitCode) {  
         std::cout << exitCode << std::endl;  
      });  
  
      io.run();  
   }  
   catch (const std::exception &e) {  
      std::cerr << e.what() << std::endl;  
   }  
  
   return 0;  
}  
```  
  
Thanks in advance for any assistance you can provide.

---

## Comment 1

> Username: mike-5345  
> Created at: 2025-03-06 16:45:02 UTC  
> Url: https://github.com/boostorg/process/issues/466#issuecomment-2704383307  

I would like to add that the goal here is to be able to terminate a child process and all of its descendent processes. I was thinking of using the initializers to set a new process group id on start so that I can terminate the whole process as a group from the parent. If there is a better way of doing this please let me know.  
  
Thanks.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2025-03-10 15:08:18 UTC  
> Url: https://github.com/boostorg/process/issues/466#issuecomment-2710921193  

You need to return `boost::system::error_code` and you should check if `BOOST_PROCESS_USE_STD_FS` is defined.  
  
Otherwise looks correct.

---

## Comment 3

> Username: bertwim  
> Created at: 2025-11-17 22:32:42 UTC  
> Updated at: 2025-11-18 11:13:59 UTC  
> Url: https://github.com/boostorg/process/issues/466#issuecomment-3544132575  

Hello,   
  
In trying to understand the boost::process API, I ran into the same issue as mike-5345, with (essentially) the same code.  Despite the suggestions given above, the issue is *not* resolved but another problem, more serious,  emerged:  
  
In short:   
- `BOOST_PROCESS_USE_STD_FS`  *not* defined: the `CustomInit` templates are *not* being used but the process executes normally and terminates successfully.    
  
- `BOOST_PROCESS_USE_STD_FS` *is* defined: the `CustomInit` templates are being used, but after "on_success",  follows an 'illegal instruction'.  
   By stepping through the debugger I  found that the problem occurs in the routine `invoke_on_success` in the file `.../boost/v2/posix/default_launcher.hpp`  
```  
template<typename Launcher, typename Init>  
inline auto invoke_on_success(Launcher & launcher, const filesystem::path &executable,  
                              const char * const * (&cmd_line),  
                              Init && init, derived && )  
-> decltype(init.on_success(launcher, executable, cmd_line))  
{  
    init.on_success(launcher, executable, cmd_line);     <<==== This line is where the problem occurs  
}**  
  
```  
  
I tried this with both g++ (15.2.1) and clang++ (21.1.6), -std=c++23. Same results.  
Is there any suggestion how to tackle this problem?

---

## Comment 4

> Username: bertwim  
> Created at: 2025-11-18 13:43:53 UTC  
> Url: https://github.com/boostorg/process/issues/466#issuecomment-3547711053  

I found the solution. There is a bug in the routine  `invoke_on_success`: According to its definition, the routine is expected to return a value, but there is no return statement:  
```  
template<typename Launcher, typename Init>  
inline auto invoke_on_success(Launcher & launcher, const filesystem::path &executable,  
                              const char * const * (&cmd_line),  
                              Init && init, derived && )  
-> decltype(init.on_success(launcher, executable, cmd_line))  
{  
    init.on_success(launcher, executable, cmd_line);  
}  
```  
When a `return`  is added in front of the call to ` init.on_success` the problem of the 'illegal instruction' is gone.  
  
```  
template<typename Launcher, typename Init>  
inline auto invoke_on_success(Launcher & launcher, const filesystem::path &executable,  
                              const char * const * (&cmd_line),  
                              Init && init, derived && )  
-> decltype(init.on_success(launcher, executable, cmd_line))  
{  
    return init.on_success(launcher, executable, cmd_line);  
}  
```
