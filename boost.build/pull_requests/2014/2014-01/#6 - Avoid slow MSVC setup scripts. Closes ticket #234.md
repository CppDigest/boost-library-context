# #6 Avoid slow MSVC setup scripts. Closes ticket #234. [Closed]

> Username: jivancic  
> Created at: 2014-01-14 16:04:24 UTC  
> Updated at: 2021-10-02 22:35:57 UTC  
> Closed at: 2014-06-27 08:27:01 UTC  
> Url: https://github.com/boostorg/build/pull/6  

Since MSVC 10.0 compiler setup scripts became very slow. Avoid calling them on every build action by caching their environment changes.  
  
Discussion: http://lists.boost.org/boost-build/2013/09/27000.php  
Ticket: https://trac.lvk.cs.msu.su/boost.build/ticket/234

---

## Comment 1

> Username: jhunold  
> Created_at: 2014-06-16 09:54:11 UTC  
> Url: https://github.com/boostorg/build/pull/6#issuecomment-46160042  

I've tested the patch(es) with msvc-12.0. No problems with standalone compilation though Incredibuild hiccups. +1 for merging from my side. Any objections?

---

## Comment 2

> Username: jivancic  
> Created_at: 2014-06-16 10:32:59 UTC  
> Updated_at: 2014-06-16 10:35:38 UTC  
> Url: https://github.com/boostorg/build/pull/6#issuecomment-46163242  

There is still the over-verbosity issue on compile/link error. You will get a huge listing of 'SET X=Y' commands, which obfuscates the error and is quite irritating. I doubt that will fly with Boost developers. Chris Nogradi offered to fix the issue, but that was 3 months ago. See the discussion thread cited above.

---

## Comment 3

> Username: jivancic  
> Created_at: 2014-06-27 08:30:17 UTC  
> Url: https://github.com/boostorg/build/pull/6#issuecomment-47319682  

Added a new pull request https://github.com/boostorg/build/pull/16

---
