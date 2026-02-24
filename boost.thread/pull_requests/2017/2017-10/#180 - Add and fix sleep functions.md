# #180 Add and fix sleep functions [Merged]

> Username: austin-beer  
> Created at: 2017-10-13 18:07:01 UTC  
> Updated at: 2017-10-16 14:42:01 UTC  
> Merged at: 2017-10-15 16:27:20 UTC  
> Closed at: 2017-10-15 16:27:20 UTC  
> Url: https://github.com/boostorg/thread/pull/180  

- Added no_interruption_point::sleep() functions to pthreads to be  
  consistent with Windows.  
- Fixed an issue where the no_interruption_point::sleep_*() functions  
  were still interruptible on Windows.  
  
In order to support these changes, I moved all of the Chrono sleep functions that were in `v2/thread.hpp` into `pthread/thread_data.hpp` and `win32/thread_data.hpp`.

---

## Comment 1

> Username: viboes  
> Created_at: 2017-10-13 19:11:29 UTC  
> Url: https://github.com/boostorg/thread/pull/180#issuecomment-336542437  

Good idea.

---
