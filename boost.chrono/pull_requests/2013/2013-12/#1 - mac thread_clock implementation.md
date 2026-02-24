# #1 mac thread_clock implementation [Merged]

> Username: ccbrown  
> Created at: 2013-12-12 05:49:31 UTC  
> Updated at: 2014-07-16 17:43:23 UTC  
> Merged at: 2013-12-19 07:19:08 UTC  
> Closed at: 2013-12-19 07:19:08 UTC  
> Url: https://github.com/boostorg/chrono/pull/1  

The chrono library seems to be missing thread_clock support for mac (and the posix implementation doesn't work for it).  This is an implementation to add support using the mach / thread_info api.  It should work on Mac 10.4 / iOS 2.0 and higher.    
  
I was pretty surprised this didn't already exist, but haven't been able to find anything in the mailing list or trac regarding it.  Curious to hear if there was a reason to avoid this implementation or if it's just something no one got around to.

---

## Comment 1

> Username: viboes  
> Created_at: 2013-12-12 07:17:03 UTC  
> Url: https://github.com/boostorg/chrono/pull/1#issuecomment-30393835  

Thanks for this PR.

---
