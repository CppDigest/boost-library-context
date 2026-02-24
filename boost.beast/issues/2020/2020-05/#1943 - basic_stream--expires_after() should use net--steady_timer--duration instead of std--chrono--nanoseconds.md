# #1943 - basic_stream::expires_after() should use net::steady_timer::duration instead of std::chrono::nanoseconds [Closed]

> Username: spdw  
> Created at: 2020-05-06 16:27:28 UTC  
> Updated at: 2020-08-20 13:27:56 UTC  
> Closed at: 2020-06-01 16:13:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1943  

Boost beast uses `std::chrono::nanoseconds` for the `expires_after()` function to set a timeout. On some operating systems the resolution of the steady clock has lower resolution (e.g., just microseconds), so the code does not compile.  
  
The patch below would fix the issue by using the steady clock duration type `net::steady_timer::duration` for the boost beast time resolution in `expires_after()` . For `expires_at()` it is already `net::steady_timer::time_point`.  
  
```  
diff --git a/boost/beast/core/basic_stream.hpp b/boost/beast/core/basic_stream.hpp  
index 25076059e..265489f91 100644  
--- a/boost/beast/core/basic_stream.hpp  
+++ b/boost/beast/core/basic_stream.hpp  
@@ -439,7 +439,7 @@ public:  
     */  
     void  
     expires_after(  
-        std::chrono::nanoseconds expiry_time);  
+        net::steady_timer::duration expiry_time);  
  
     /** Set the timeout for the next logical operation.  
  
diff --git a/boost/beast/core/impl/basic_stream.hpp b/boost/beast/core/impl/basic_stream.hpp  
index 336c09f22..f4e6031da 100644  
--- a/boost/beast/core/impl/basic_stream.hpp  
+++ b/boost/beast/core/impl/basic_stream.hpp  
@@ -697,7 +697,7 @@ release_socket() ->  
template<class Protocol, class Executor, class RatePolicy>  
void  
basic_stream<Protocol, Executor, RatePolicy>::  
-expires_after(std::chrono::nanoseconds expiry_time)  
+expires_after(net::steady_timer::duration expiry_time)  
{  
     // If assert goes off, it means that there are  
     // already read or write (or connect) operations  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-05-06 16:33:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1943#issuecomment-624755009  

Hi @spdw,  
  
 Good catch. Thank you.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-05-06 16:52:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1943#issuecomment-624765082  

I don't agree with this. Beast is supposed to be portable. If we accept this change, then users could write code that compiles on some platforms but not others. I agree that there's a bug here but the fix needs to be different. We should change the parameter to `std::chrono::milliseconds` and then use `duration_cast` to round it up to the nearest whole unit represented by the steady clock. This will fix the compilation error, and Beast users will be able to continue writing code that compiles on all platforms.

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-05-06 18:01:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1943#issuecomment-624801080  

Should we mimic the behaviour of asio here?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-05-06 18:06:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1943#issuecomment-624803553  

> Should we mimic the behaviour of asio here?  
  
Can you be explicit? Example code?

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-05-21 12:31:01 UTC  
> Updated at: 2020-05-21 12:44:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1943#issuecomment-632059612  

Asio's `system_timer` uses the `duration_type` of the `system_clock`:  
  
https://www.boost.org/doc/libs/1_73_0/doc/html/boost_asio/reference/basic_waitable_timer/duration.html  
  
Asio's `steady_timer` is a typedef: `typedef basic_waitable_timer< chrono::steady_clock > steady_timer`  
ref: https://www.boost.org/doc/libs/1_73_0/doc/html/boost_asio/reference/steady_timer.html  
  
Thus, asio::steady_timer::expires_after() takes a steady_clock::duration as an argument.  
  
If we took the same type the benefits would be:  
  
1. Users' reasonable expectation that our API matches asio/net.ts is satisfied.  
2. durations passed by users will implicitly cast provided they are at least as precise as durations of the system's `steady_clock`, which is to say either milliseconds or nanoseconds depending on whether you're on OSX or linux/windows.  
  
Background:  
std::chrono::duration types will implictily cast to more accurate duration types, but they must be explicitly cast to less accurate durations where there is a risk of loss of precision.  
  
std::chrono places no restrictions on the accuracy of a clock's duration. It would be perfectly permissible to produce a standard library where the resolution was 1 second.  
  
If we standardise on milliseconds rather than clock_type::duration, then we just hit the same problem.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-05-21 12:54:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1943#issuecomment-632069487  

> If we standardise on milliseconds rather than clock_type::duration, then we just hit the same problem.  
  
No, because we can simply use duration cast and round up to the next integral value that can be represented by the target clock. Yes it is true that in theory the resolution could be 1 second, it is not true in practice. But even if it was, having the implementation perform integral ceil in order to convert will eliminate the issue.
