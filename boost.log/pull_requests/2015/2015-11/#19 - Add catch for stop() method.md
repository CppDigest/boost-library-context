# #19 Add catch for stop() method. [Closed]

> Username: ya1gaurav  
> Created at: 2015-11-03 04:56:01 UTC  
> Updated at: 2015-11-05 04:05:45 UTC  
> Closed at: 2015-11-04 10:27:31 UTC  
> Url: https://github.com/boostorg/log/pull/19  

stop() method throws as below:  
            catch (...)  
            {  
                m_StopRequested.store(false, boost::memory_order_release);  
                throw;  
            }  
the try-catch block is missing when stop is called.

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-11-03 06:44:54 UTC  
> Url: https://github.com/boostorg/log/pull/19#issuecomment-153260232  

What is your use case when `stop()` throws? From what I can see, if it throws then either the mutex/condition variable are broken or joining the feeding thread fails. In either case the state of the program is corrupted and it's better to terminate the program anyway.

---

## Comment 2

> Username: ya1gaurav  
> Created_at: 2015-11-03 08:03:19 UTC  
> Url: https://github.com/boostorg/log/pull/19#issuecomment-153283568  

This is reported by some static analyzer tool.

---

## Comment 3

> Username: Lingxi-Li  
> Created_at: 2015-11-04 07:20:50 UTC  
> Updated_at: 2015-11-04 07:38:13 UTC  
> Url: https://github.com/boostorg/log/pull/19#issuecomment-153613199  

Propagating the exception, if any, is fine to me. In my opinion, typically, when a clean-up call fails, there is nothing you can do about it but to terminate the corresponding module or the whole program. Most such calls just return `void`. Windows API is different. Calls like `CloseHandle` may return `BOOL` to indicate success or failure. I think this is mainly for debugging purposes.

---

## Comment 4

> Username: ya1gaurav  
> Created_at: 2015-11-04 08:55:14 UTC  
> Url: https://github.com/boostorg/log/pull/19#issuecomment-153644032  

how about throwing in debug mode only ?

---

## Comment 5

> Username: Lingxi-Li  
> Created_at: 2015-11-04 09:00:15 UTC  
> Url: https://github.com/boostorg/log/pull/19#issuecomment-153645586  

Typically, clean-up calls should never fail. But if one does fail, it usually implies a catastrophic error. In such cases, it may be reasonable to inform the user about it so he/she may take actions accordingly, such as restart the program. Suppress and ignore a catastrophic error may not be a wise option.

---

## Comment 6

> Username: Lastique  
> Created_at: 2015-11-04 09:01:22 UTC  
> Url: https://github.com/boostorg/log/pull/19#issuecomment-153645921  

@ya1gaurav: No, I think this kind of behavior should not depend on the build options.  
  
I'm currently deciding between leaving the code as is and calling `std::terminate()` on any exceptions in the destructor. There's also an option to make `stop()` `noexcept` and call `std::terminate()` there, but that is concealing the problem when the function is called by the user.

---

## Comment 7

> Username: Lingxi-Li  
> Created_at: 2015-11-04 09:15:00 UTC  
> Updated_at: 2015-11-04 09:22:08 UTC  
> Url: https://github.com/boostorg/log/pull/19#issuecomment-153648231  

I guess it is fine to mark it as `noexcept`. Just consider `operator delete` in C++. It is marked `noexcept`. But it may actually fail, for example, when the heap is corrupted. In such cases, the behavior is undefined. It may crash the program immediately or let it go but in a corrupted and unknown state and crash at a later time. Calling `std::terminate()` there at least makes the behavior in such cases well defined. Personally, I prefer leaving the code as it is, propagating the exception and leaving the hard choice to the programmer :P

---

## Comment 8

> Username: Lingxi-Li  
> Created_at: 2015-11-04 09:33:26 UTC  
> Url: https://github.com/boostorg/log/pull/19#issuecomment-153656751  

I see that the call is made in a destructor. I think it is appropriate to _leave the code as is and calling `std::terminate()` on any exceptions in the destructor._

---

## Comment 9

> Username: Lastique  
> Created_at: 2015-11-04 10:27:31 UTC  
> Url: https://github.com/boostorg/log/pull/19#issuecomment-153673866  

Thing is, the destructor is only marked `noexcept` in C++11, and even then some compilers do not mark it implicitly. So to make the behavior the same for all compilers I've added an explicit call to `std::terminate` in https://github.com/boostorg/log/commit/fd3e82a6108256ff0305046afbe2f63de79fd880.

---

## Comment 10

> Username: ya1gaurav  
> Created_at: 2015-11-05 04:05:45 UTC  
> Url: https://github.com/boostorg/log/pull/19#issuecomment-153949824  

Thanks for discussion :)

---
