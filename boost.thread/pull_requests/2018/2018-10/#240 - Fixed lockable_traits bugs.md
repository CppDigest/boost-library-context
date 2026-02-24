# #240 Fixed lockable_traits bugs [Merged]

> Username: Kojoley  
> Created at: 2018-10-04 14:28:00 UTC  
> Updated at: 2018-10-07 17:09:24 UTC  
> Merged at: 2018-10-07 17:07:49 UTC  
> Closed at: 2018-10-07 17:07:50 UTC  
> Url: https://github.com/boostorg/thread/pull/240  

Traits does not handle `noexcept` qualifiers (which are part of function type in C++17) and wrongly did not detect methods.  
Because of this on MSVC 14.1 with -std=c++17 `boost::is_mutex_type<boost::mutex>::value` is false and `boost::lock(mutex)` does not compile.  
  
Fixes #222.

---

## Comment 1

> Username: Kojoley  
> Created_at: 2018-10-04 14:48:39 UTC  
> Url: https://github.com/boostorg/thread/pull/240#issuecomment-427047921  

Hm, there is no boost macro to detect P0012R1, I think that it would be better to switch to decltype based detection.

---

## Comment 2

> Username: viboes  
> Created_at: 2018-10-05 05:23:48 UTC  
> Url: https://github.com/boostorg/thread/pull/240#issuecomment-427249882  

Hi, have you checked on Windows? I see yet a lot of issues on the appVeyor.

---

## Comment 3

> Username: Kojoley  
> Created_at: 2018-10-05 11:25:38 UTC  
> Url: https://github.com/boostorg/thread/pull/240#issuecomment-427333432  

The PR has nothing with issues on the Appveyor which are transient. You can try to solve them by adjusting tests timeout, running only two jobs simultaneously (appveyor gives a vm with two cores, but b2 is invoked with `-j3`) or perform multiple tries before failing on those timing critical tests.

---

## Comment 4

> Username: viboes  
> Created_at: 2018-10-07 16:59:30 UTC  
> Url: https://github.com/boostorg/thread/pull/240#issuecomment-427668754  

I'm wondering to remove those unstable test (depending on the time) from the  regression tests and execute them only on machines I master.  
  
On the other side I Would need to add tests that replace the real time and this needs a lot of work.

---
