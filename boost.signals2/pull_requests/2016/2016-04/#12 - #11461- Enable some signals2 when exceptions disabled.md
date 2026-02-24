# #12 #11461: Enable some signals2 when exceptions disabled [Merged]

> Username: DigitalDan05  
> Created at: 2016-04-29 21:47:07 UTC  
> Updated at: 2016-04-30 20:46:20 UTC  
> Merged at: 2016-04-30 20:46:20 UTC  
> Closed at: 2016-04-30 20:46:20 UTC  
> Url: https://github.com/boostorg/signals2/pull/12  

This allows signals2 to be used when exception support is disabled by  
removing certain functionality. If that functionality is used it should  
give a compilation error and can be fixed at that time or another  
workaround used.  
  
This gives a partial fix for issue https://svn.boost.org/trac/boost/ticket/11461  
  
I have an interest in making signals2 work without exceptions, but it wasn't clear what needed to be done.  I started by removing the offending throw function, and didn't see a missing function error, so I figured at least some of the functionality could run without having exceptions.  
  
I would like any comments about this, how I could improve it and/or what functionality is broken by it.

---

## Comment 1

> Username: fmhess  
> Created_at: 2016-04-30 20:44:42 UTC  
> Url: https://github.com/boostorg/signals2/pull/12#issuecomment-215993742  

At this point, it looks like I don't throw expired_slot internally any more, except when using signals as slots.  Even this looks like it is not needed any more.  So, I think I'm going to merge your changes and then I'll clean up the throws in signal_template.hpp

---
