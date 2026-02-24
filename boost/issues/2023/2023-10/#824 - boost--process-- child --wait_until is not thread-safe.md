# #824 - [Linux] boost::process:: child ::wait_until is not thread-safe [Closed]

> Username: AlexeyZaharov  
> Created at: 2023-10-29 16:13:10 UTC  
> Updated at: 2023-10-30 09:14:52 UTC  
> Closed at: 2023-10-30 09:14:52 UTC  
> Url: https://github.com/boostorg/boost/issues/824  

Hi! I faced with the problem that my process hangs up while it uses _wait_until()_ of child::process. So I watched boost code and find that **boost::process::child::wait_until is not thread-safe**  
  
1. _struct signal_interceptor_t_  is used for [setting](https://github.com/boostorg/process/blob/1873f34435b87f99c2073543bf9f5d67f74cbdb8/include/boost/process/detail/posix/wait_for_exit.hpp#L64-L74) custom reaction on signal with using system _::signal_. So _handler_func_ is a new handler and _sigchld_handler_ is setting for previous handler  
2. The problem is that signal handler - **atribut of the process, not thread**, so if the next thread call ::signal then _sigchld_handler_ is setting for _handler_func_  
3. Which thread will be used for handle signal is undefined. So thread with  _sigchld_handler_ _==_ _handler_func_ can be used! And this cause infinite loop  
4. You can face with the problem in this code, which uses problem part of _wait_until_. (On my system signals are handled by main thread). Also attach out of the programm  
[bug_wait_until.cpp.txt](https://github.com/boostorg/boost/files/13198558/bug_wait_until.cpp.txt)  
[out.txt](https://github.com/boostorg/boost/files/13198559/out.txt)  
  
So can you update your documentation with this note please?

---

## Comment 1

> Username: AlexeyZaharov  
> Created at: 2023-10-30 09:14:52 UTC  
> Url: https://github.com/boostorg/boost/issues/824#issuecomment-1784776069  

Close this issue and open in [right place](https://github.com/boostorg/process/issues/347)
