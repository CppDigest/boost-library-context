# #4 Extend version-countdown to also support Python 3.1-3.4. [Merged]

> Username: forderud  
> Created at: 2013-12-15 12:21:33 UTC  
> Updated at: 2021-10-02 22:36:04 UTC  
> Merged at: 2013-12-16 10:30:01 UTC  
> Closed at: 2013-12-16 10:30:01 UTC  
> Url: https://github.com/boostorg/build/pull/4  

As of today, Boost Python does not build against Python >3.0 on Windows. This is because newer installations are not detected by the windows-installed-pythons rule. The reason for this is because the rule only looks for Python 1.5-3.0, and not for newer versions.  
  
This patch extends version-countdown used by windows-installed-pythons to also search for Python 3.1-3.4. This fixes the problem of building Boost Python against Python 3.3 on computer.

---
