# #232 - GetCurrentThreadId is called everytime even not extracting current_thread_id [Closed]

> Username: UMU618  
> Created at: 2024-07-09 13:42:44 UTC  
> Updated at: 2024-12-19 21:35:04 UTC  
> Closed at: 2024-08-08 16:32:43 UTC  
> Url: https://github.com/boostorg/log/issues/232  

I use API monitor to monitoring kernel32!GetCurrentThreadId, and found out it has been called everytime log, even not extracting current_thread_id.  
  
PS: extracting `current_process_id` has no problem.

---

## Comment 1

> Username: Lastique  
> Created at: 2024-07-09 14:29:49 UTC  
> Url: https://github.com/boostorg/log/issues/232#issuecomment-2217890682  

`GetCurrentThreadId` could be called from anywhere, including the standard library. Is there a problem with it being called? Did you collect any backtraces from where it is called?

---

## Comment 2

> Username: UMU618  
> Created at: 2024-07-13 16:20:40 UTC  
> Url: https://github.com/boostorg/log/issues/232#issuecomment-2226983013  

Refer to folly, `GetCurrentThreadId` is slow.  
https://github.com/facebook/folly/blob/2d9c0a3c720238ae2ef1337348b6a72778024961/folly/system/ThreadId.cpp#L41

---

## Comment 3

> Username: UMU618  
> Created at: 2024-07-13 17:02:56 UTC  
> Url: https://github.com/boostorg/log/issues/232#issuecomment-2226997351  

It's called from a `boost::log::aux::exclusive_lock_guard` in function `feed_record` file `boost\log\sinks\basic_sink_frontend.hpp`  
  
```cpp  
// Feed the record  
BOOST_LOG_EXPR_IF_MT(boost::log::aux::exclusive_lock_guard< BackendMutexT > lock(backend_mutex);)  
backend.consume(rec, context->m_FormattedRecord);  
```

---

## Comment 4

> Username: UMU618  
> Created at: 2024-07-13 17:24:05 UTC  
> Url: https://github.com/boostorg/log/issues/232#issuecomment-2227002141  

I believe it originates from Boost.Thread.  
  
boost::recursive_mutex *backend_mutex;  
  
![image](https://github.com/user-attachments/assets/b00d4ced-98fb-4381-891d-d05aca4dc8f1)

---

## Comment 5

> Username: Lastique  
> Created at: 2024-07-13 17:51:14 UTC  
> Url: https://github.com/boostorg/log/issues/232#issuecomment-2227010911  

> Refer to folly, `GetCurrentThreadId` is slow.  
  
That link does not indicate that `GetCurrentThreadId` is slow. It does cache its value in one code path, but it also provides `getCurrentThreadID` that uses `GetCurrentThreadId` on Windows that doesn't cache its value.  
  
Do you have any profiling data that shows that `GetCurrentThreadId` is indeed a problem? If so, I would like to see the benchmark.  
  
> I believe it originates from Boost.Thread.  
  
You should probably report it [there](https://github.com/boostorg/thread/issues), along with the benchmark, and, if possible, your suggestions on optimizing it.

---

## Comment 6

> Username: Lastique  
> Created at: 2024-07-13 18:01:27 UTC  
> Url: https://github.com/boostorg/log/issues/232#issuecomment-2227033390  

BTW, see these:  
  
https://stackoverflow.com/a/15008330/4636534  
https://stackoverflow.com/a/12909560/4636534  
  
So I seriously doubt `GetCurrentThreadId` is the problem.

---

## Comment 7

> Username: UMU618  
> Created at: 2024-08-08 01:31:06 UTC  
> Url: https://github.com/boostorg/log/issues/232#issuecomment-2274744259  

The cached impl from folly is 2x faster then the API in release configuration.

---

## Comment 8

> Username: Lastique  
> Created at: 2024-08-08 16:32:43 UTC  
> Url: https://github.com/boostorg/log/issues/232#issuecomment-2276229087  

Here's a benchmark for different types of mutexes:  
  
[test_mutex.cpp.txt](https://github.com/user-attachments/files/16550846/test_mutex.cpp.txt)  
  
Compile with MSVC 14.3:  
  
```  
cl /O2 /EHsc /I . /DNDEBUG /MD test_mutex.cpp /link /LIBPATH:stage\lib  
```  
  
On Windows 10, on my system it produces these results:  
  
```  
boost::recursive_mutex: 1000000 iterations, 1 threads, 17603 us, 5.68085e+07 iterations per second  
boost::recursive_mutex: 2000000 iterations, 2 threads, 58395 us, 3.42495e+07 iterations per second  
boost::recursive_mutex: 4000000 iterations, 4 threads, 115647 us, 3.4588e+07 iterations per second  
boost::recursive_mutex: 8000000 iterations, 8 threads, 243507 us, 3.28533e+07 iterations per second  
std::recursive_mutex: 1000000 iterations, 1 threads, 14036 us, 7.12454e+07 iterations per second  
std::recursive_mutex: 2000000 iterations, 2 threads, 25280 us, 7.91139e+07 iterations per second  
std::recursive_mutex: 4000000 iterations, 4 threads, 57344 us, 6.97545e+07 iterations per second  
std::recursive_mutex: 8000000 iterations, 8 threads, 154457 us, 5.17944e+07 iterations per second  
critical_section(0): 1000000 iterations, 1 threads, 11456 us, 8.72905e+07 iterations per second  
critical_section(0): 2000000 iterations, 2 threads, 91314 us, 2.19024e+07 iterations per second  
critical_section(0): 4000000 iterations, 4 threads, 307022 us, 1.30284e+07 iterations per second  
critical_section(0): 8000000 iterations, 8 threads, 1001230 us, 7.99017e+06 iterations per second  
critical_section(10): 1000000 iterations, 1 threads, 11411 us, 8.76347e+07 iterations per second  
critical_section(10): 2000000 iterations, 2 threads, 65629 us, 3.04743e+07 iterations per second  
critical_section(10): 4000000 iterations, 4 threads, 156023 us, 2.56372e+07 iterations per second  
critical_section(10): 8000000 iterations, 8 threads, 473790 us, 1.68851e+07 iterations per second  
critical_section(100): 1000000 iterations, 1 threads, 11058 us, 9.04323e+07 iterations per second  
critical_section(100): 2000000 iterations, 2 threads, 104436 us, 1.91505e+07 iterations per second  
critical_section(100): 4000000 iterations, 4 threads, 247020 us, 1.6193e+07 iterations per second  
critical_section(100): 8000000 iterations, 8 threads, 877609 us, 9.11568e+06 iterations per second  
critical_section(1000): 1000000 iterations, 1 threads, 11201 us, 8.92777e+07 iterations per second  
critical_section(1000): 2000000 iterations, 2 threads, 93654 us, 2.13552e+07 iterations per second  
critical_section(1000): 4000000 iterations, 4 threads, 307327 us, 1.30155e+07 iterations per second  
critical_section(1000): 8000000 iterations, 8 threads, 1252495 us, 6.38725e+06 iterations per second  
```  
  
So, `boost::recursive_mutex` is a close second after `std::recursive_mutex`. Note that `std::recursive_mutex` also [uses](https://github.com/microsoft/STL/blob/938bd5986189da29d45eaf2ba0053f1656cd54a3/stl/src/mutex.cpp#L78) `GetCurrentThreadId` internally.  
  
Considering that the test exhibits much stronger contention than what is expected in Boost.Log, and literally tests mutex performance with next to no other useful code, I expect all of these mutex types to perform roughly the same in the real application. Still, since C++11 is now the minimum, I might switch to `std::recursive_mutex` in the future.  
  
I'm not sure what your problem is with `GetCurrentThreadId`, but I don't see it as a performance problem. And I don't see what I could do about it in the context of Boost.Log anyway. If you can suggest a more optimal implementation of `boost::recursive_mutex`, please file a PR for [Boost.Thread](https://github.com/boostorg/thread).

---

## Comment 9

> Username: UMU618  
> Created at: 2024-12-17 14:46:55 UTC  
> Url: https://github.com/boostorg/log/issues/232#issuecomment-2548649644  

On Windows 11, AMD 8845HS:  
  
```  
boost::recursive_mutex: 1000000 iterations, 1 threads, 7580 us, 1.31926e+08 iterations per second  
boost::recursive_mutex: 2000000 iterations, 2 threads, 24824 us, 8.05672e+07 iterations per second  
boost::recursive_mutex: 4000000 iterations, 4 threads, 44951 us, 8.89858e+07 iterations per second  
boost::recursive_mutex: 8000000 iterations, 8 threads, 85266 us, 9.3824e+07 iterations per second  
std::recursive_mutex: 1000000 iterations, 1 threads, 6547 us, 1.52742e+08 iterations per second  
std::recursive_mutex: 2000000 iterations, 2 threads, 15779 us, 1.26751e+08 iterations per second  
std::recursive_mutex: 4000000 iterations, 4 threads, 52052 us, 7.68462e+07 iterations per second  
std::recursive_mutex: 8000000 iterations, 8 threads, 321942 us, 2.48492e+07 iterations per second  
critical_section(0): 1000000 iterations, 1 threads, 4866 us, 2.05508e+08 iterations per second  
critical_section(0): 2000000 iterations, 2 threads, 11032 us, 1.81291e+08 iterations per second  
critical_section(0): 4000000 iterations, 4 threads, 48486 us, 8.2498e+07 iterations per second  
critical_section(0): 8000000 iterations, 8 threads, 309092 us, 2.58823e+07 iterations per second  
critical_section(10): 1000000 iterations, 1 threads, 6072 us, 1.6469e+08 iterations per second  
critical_section(10): 2000000 iterations, 2 threads, 26434 us, 7.56601e+07 iterations per second  
critical_section(10): 4000000 iterations, 4 threads, 56463 us, 7.08429e+07 iterations per second  
critical_section(10): 8000000 iterations, 8 threads, 190943 us, 4.18973e+07 iterations per second  
critical_section(100): 1000000 iterations, 1 threads, 4939 us, 2.0247e+08 iterations per second  
critical_section(100): 2000000 iterations, 2 threads, 20777 us, 9.62603e+07 iterations per second  
critical_section(100): 4000000 iterations, 4 threads, 54023 us, 7.40425e+07 iterations per second  
critical_section(100): 8000000 iterations, 8 threads, 188577 us, 4.2423e+07 iterations per second  
critical_section(1000): 1000000 iterations, 1 threads, 3597 us, 2.78009e+08 iterations per second  
critical_section(1000): 2000000 iterations, 2 threads, 10880 us, 1.83824e+08 iterations per second  
critical_section(1000): 4000000 iterations, 4 threads, 50896 us, 7.85916e+07 iterations per second  
critical_section(1000): 8000000 iterations, 8 threads, 271605 us, 2.94545e+07 iterations per second  
```  
  
When there are 4+ threads, boost::recursive_mutex is faster.

---

## Comment 10

> Username: Lastique  
> Created at: 2024-12-17 15:40:21 UTC  
> Url: https://github.com/boostorg/log/issues/232#issuecomment-2548793836  

What compiler and compiler options were you using?

---

## Comment 11

> Username: UMU618  
> Created at: 2024-12-19 09:45:41 UTC  
> Url: https://github.com/boostorg/log/issues/232#issuecomment-2553232154  

MSVC, C++20, MD Release

---

## Comment 12

> Username: Lastique  
> Created at: 2024-12-19 21:29:46 UTC  
> Updated at: 2024-12-19 21:35:04 UTC  
> Url: https://github.com/boostorg/log/issues/232#issuecomment-2555806057  

I can't test it myself on an AMD system, so I can't explain your results with any confidence. My only theory is that it could be related to the performance profile Windows 11 uses on your machine. It seems strange that `std::recursive_mutex` @ 8 threads is the worst option in your case and the best one in mine.
