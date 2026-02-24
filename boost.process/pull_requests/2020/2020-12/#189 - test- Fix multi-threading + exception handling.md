# #189 test: Fix multi-threading + exception handling [Closed]

> Username: dkl  
> Created at: 2020-12-08 21:28:27 UTC  
> Updated at: 2021-10-15 17:47:56 UTC  
> Closed at: 2021-10-15 04:39:51 UTC  
> Url: https://github.com/boostorg/process/pull/189  

If a unit test throws an exception, usually the test framework will catch it cleanly. However, for multi-threaded tests, the test code needs to catch exceptions explicitly, because apparently the Boost Unit Test Framework is not thread-safe.  
  
Here I've seen the multithreaded_async_pipe unit test triggering multiple exceptions in parallel (because the async_pipe constructor failed), which resulted in multiple parallel std::terminate() calls. Then each std::terminate() triggered a SIGABRT for that thread, causing each thread to invoke the Boost Unit Test Framework's signal handler (boost_execution_monitor_jumping_signal_handler()), which however is not thread-safe (doing the same longjmp() in all threads, resulting in stack corruption, SIGSEGV, sometimes even hangs).  
  
As solution, I think using std::future should work – it supports transporting the exceptions from the thread to the parent and will re-throw them there (but note that obviously we'll only see the 1st, not all exceptions from all threads).  
  
Although I've only had problems with the multithreaded_async_pipe test, and that was essentially only triggered by coincidence (it tries to create N_cores * 100 pipes, which fails here due to hitting the maximum number of open files soft-limit (ulimit -n, 1024)), I still think it's worth it to fix the test suite behaviour in this case, especially because it sometimes ended up hanging to the stack smashing. Thus I've also converted all other unit tests from std::thread to std::future + std::async.

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2021-10-14 06:29:34 UTC  
> Url: https://github.com/boostorg/process/pull/189#issuecomment-943011940  

What is this addressing? A false negative or just a crash on test failure?

---

## Comment 2

> Username: dkl  
> Created_at: 2021-10-14 18:47:47 UTC  
> Url: https://github.com/boostorg/process/pull/189#issuecomment-943627360  

The main problem is undefined behaviour in the test suite, in case of multi-threading and exceptions. It could cause the test suite to randomly pass, fail, print garbage messages, crash, or even hang. Obviously that's not great for debugging, regardless of whether the actual error that has happened is a false-positive or real bug. In this patch I tried to fix all potential cases which could run into that problem, to ensure the test suite has consistent behaviour.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2021-10-15 02:50:16 UTC  
> Url: https://github.com/boostorg/process/pull/189#issuecomment-943942832  

I think adding noexcept to the threads is better, causing a reliable `std::terminate` .

---

## Comment 4

> Username: dkl  
> Created_at: 2021-10-15 17:47:56 UTC  
> Url: https://github.com/boostorg/process/pull/189#issuecomment-944484359  

Interesting idea; if it works, it should be applied to all the threads in the test suite, not only in `test/async.cpp`. (thinking of `test/async_pipe.cpp`, which is where I saw the problem)  
  
However I'm not sure about `std::terminate`. As mentioned in the description, multiple parallel `std::terminate` was part of the problem (something weird about it using abort()/SIGABRT internally, causing it to invoke the test framework's non-thread-safe signal handler...). I tried `std::future` with `std::launch::async` specifically to avoid the multiple parallel `std::terminate` calls. (`std::thread::~thread` calls `std::terminate` if the thread is still running during stack unwinding, while `std::future` from `std::launch::async` waits for the thread to finish)

---
