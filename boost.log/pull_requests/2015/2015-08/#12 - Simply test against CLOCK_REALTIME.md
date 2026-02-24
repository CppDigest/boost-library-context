# #12 Simply test against CLOCK_REALTIME. [Closed]

> Username: EdSchouten  
> Created at: 2015-08-27 13:21:43 UTC  
> Updated at: 2015-08-27 13:49:12 UTC  
> Closed at: 2015-08-27 13:48:10 UTC  
> Url: https://github.com/boostorg/log/pull/12  

Just like GNU Hurd and OpenBSD, Nuxi CloudABI[1] implements  
clock_gettime(), but cannot define _POSIX_TIMERS. The reason for this is  
that CloudABI cannot implement the timer_*() interface, as it does not  
provide full UNIX signal handling support.  
  
Instead of making the checks in this source file even more complex,  
simply fall back to testing against CLOCK_REALTIME. I'd say it would be  
very unlikely for an implementation to provide CLOCK_REALTIME, but not  
offer a working clock_gettime() function that can operate on it.  
  
[1] Nuxi CloudABI: https://github.com/NuxiNL/cloudlibc

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-08-27 13:48:10 UTC  
> Url: https://github.com/boostorg/log/pull/12#issuecomment-135437226  

No, this change is not valid. POSIX defines the protocol to declare certain features as supported by the platform, and Boost.Log follows it. As an extension, it also uses POSIX clocks on platforms which do not comply to the spec completely, but do support CLOCK_REALTIME. The platforms that are supported this way are very specific and should be listed explicitly. Please, create another PR that adds Nuxi CloudABI detection to the existing check, if that platform really supports CLOCK_REALTIME.

---

## Comment 2

> Username: Lastique  
> Created_at: 2015-08-27 13:49:12 UTC  
> Url: https://github.com/boostorg/log/pull/12#issuecomment-135437650  

Also, please create PRs against the develop branch.

---
