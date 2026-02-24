# #165 Added support for getting time on Windows to the timespec classes. [Merged]

> Username: austin-beer  
> Created at: 2017-09-27 17:00:49 UTC  
> Updated at: 2017-09-28 15:11:07 UTC  
> Merged at: 2017-09-28 06:17:24 UTC  
> Closed at: 2017-09-28 06:17:24 UTC  
> Url: https://github.com/boostorg/thread/pull/165  

On Windows, it seems to me that we need to eliminate the use of GitTickCount64() and the associated `timeout` class and replace it with our custom classes in timespec.hpp. If I'm duplicating work you've already done, then just ignore this PR. Otherwise, this PR allows us to use timespec.hpp on Windows.

---

## Comment 1

> Username: MarcelRaad  
> Created_at: 2017-09-27 18:22:17 UTC  
> Url: https://github.com/boostorg/thread/pull/165#issuecomment-332612026  

> On Windows, it seems to me that we need to eliminate the use of GitTickCount64() and the associated timeout class and replace it with our custom classes in timespec.hpp  
  
Sorry I haven't followed this closely, but please make sure that there's still a way to use Boost.Thread without relying on `QueryPerformanceCounter`. It's more or less a random number generator on Windows Server 2003 with multiple CPUs (and on Windows XP with older chipsets, which I care less about). This is currently possible by using the Boost.DateTime interface and disabling Boost.Chrono usage (effectively reverting https://github.com/boostorg/thread/commit/d83d23bee0ad63eb6eeab6f51d2b49c871d367f9).

---

## Comment 2

> Username: austin-beer  
> Created_at: 2017-09-27 18:26:51 UTC  
> Url: https://github.com/boostorg/thread/pull/165#issuecomment-332613294  

Thanks for the info, Marcel. I wasn't aware of the issue with `QueryPerformanceCounter`, but that makes sense.

---

## Comment 3

> Username: viboes  
> Created_at: 2017-09-28 01:11:28 UTC  
> Url: https://github.com/boostorg/thread/pull/165#issuecomment-332698578  

Great. Thanks for this patch.  
  
So the idea now is to move it to detail and use it at least for the generic  shared_mutex.hpp and for future.hpp and then  we could adapt the specific win32 files to use it, isn't it?  
  
I'll check for the MacOs side on my side.  
  
I'll commit it as soon as the CI is finished.

---

## Comment 4

> Username: austin-beer  
> Created_at: 2017-09-28 02:33:37 UTC  
> Url: https://github.com/boostorg/thread/pull/165#issuecomment-332710209  

Yep, that's exactly what I'm thinking.  
  
I don't have access to macOS so I'm glad you do.  
  
Thanks.

---
