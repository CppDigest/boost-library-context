# #80 Do not use Windows ANSI APIs when targetting WinRT [Merged]

> Username: mauve  
> Created at: 2015-10-04 12:31:33 UTC  
> Updated at: 2015-10-04 16:21:15 UTC  
> Merged at: 2015-10-04 16:21:15 UTC  
> Closed at: 2015-10-04 16:21:15 UTC  
> Url: https://github.com/boostorg/config/pull/80  

(This is a similar PR as https://github.com/boostorg/config/pull/16 but with the difference that it doesn't pull in a dependency on Boost.Predef)  
  
When targeting WinRT (phone, store, universal) we do not want to use ANSI versions of Windows APIS as a lot of the old win32 ansi apis are missing, therefore let's skip them altogether.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-10-04 16:21:12 UTC  
> Url: https://github.com/boostorg/config/pull/80#issuecomment-145363806  

That looks better, thanks.

---
