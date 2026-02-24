# #347 - [Linux] boost::process:: child ::wait_until is not thread-safe [Open]

> Username: AlexeyZaharov  
> Created at: 2023-10-30 09:13:58 UTC  
> Updated at: 2023-12-20 00:10:18 UTC  
> Url: https://github.com/boostorg/process/issues/347  

Hi! I faced with the problem that my process hangs up while it uses _wait_until()_ of child::process. So I watched boost code and find that boost::process:: child ::wait_until is not thread-safe  
  
1. _struct signal_interceptor_t_ is used for [setting](https://github.com/boostorg/process/blob/1873f34435b87f99c2073543bf9f5d67f74cbdb8/include/boost/process/detail/posix/wait_for_exit.hpp#L64-L74) custom reaction on signal with using system _::signal_. So _handler_func_ is a new handler and _sigchld_handler_ is setting for previous handler  
2. The problem is that signal handler - **atribut of the process, not thread**, so if the next thread call ::signal then _sigchld_handler_ is setting for _handler_func_  
3. Which thread will be used for handle signal is undefined. So thread with _sigchld_handler == handler_func_ can be used! And this cause infinite loop  
4. You can face with the problem in this code, which uses problem part of _wait_until_. (On my system signals are handled by main thread). Also attach out of the programm  
[bug_wait_until.cpp.txt](https://github.com/boostorg/boost/files/13198558/bug_wait_until.cpp.txt)  
[out.txt](https://github.com/boostorg/boost/files/13198559/out.txt)  
  
So can you update your documentation with this note please?  
  
UPD: After I write this issue I found the same:  
[POSIX wait_for() crashes when multiple threads run it simultaneously](https://github.com/boostorg/process/issues/168)  
[POSIX wait_for - stack overflow](https://github.com/boostorg/process/issues/232)  
[child::wait_for() may block indefinitely](https://github.com/boostorg/process/issues/204)

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-10-30 11:09:56 UTC  
> Url: https://github.com/boostorg/process/issues/347#issuecomment-1784963411  

I deprecated them, they're generally unsafe.

---

## Comment 2

> Username: AlexeyZaharov  
> Created at: 2023-10-30 11:19:09 UTC  
> Url: https://github.com/boostorg/process/issues/347#issuecomment-1784976950  

Can you update documentation with information about deprecation please? I can`t find this info

---

## Comment 3

> Username: jens-diewald  
> Created at: 2023-11-30 14:19:12 UTC  
> Url: https://github.com/boostorg/process/issues/347#issuecomment-1833868532  

I, too, would appreciate information on the deprecation.  
Specifically, on Windows, these functions are fine, right?  
Do you plan to remove them in a future release?

---

## Comment 4

> Username: rroquetto-uptycs  
> Created at: 2023-12-19 23:43:30 UTC  
> Url: https://github.com/boostorg/process/issues/347#issuecomment-1863622807  

@klemens-morgenstern Following up on the previous comments, do you have any references on the rationale for the deprecation, so that I can better understand what the issue is and look for alternatives moving forward?  
Thanks!

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2023-12-20 00:03:32 UTC  
> Url: https://github.com/boostorg/process/issues/347#issuecomment-1863637181  

They are fine on windows, but unsafe on linux. Therefor process cant provide a portable function that is safe.  
  
The recommendation is to use V2 and async_wait, so you can us an asio  timer to create a timeout.

---

## Comment 6

> Username: rroquetto-uptycs  
> Created at: 2023-12-20 00:10:16 UTC  
> Url: https://github.com/boostorg/process/issues/347#issuecomment-1863642822  

Ah great, thank you very much for such a prompt response!
