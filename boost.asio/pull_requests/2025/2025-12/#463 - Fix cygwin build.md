# #463 Fix cygwin build. [Open]

> Username: corngood  
> Created at: 2025-12-18 14:30:06 UTC  
> Updated at: 2025-12-18 14:38:00 UTC  
> Url: https://github.com/boostorg/asio/pull/463  

This is part of a set of changes to fix boost on cygwin.  You can see the other submodules that will require changes here:  
  
https://github.com/boostorg/boost/compare/master...corngood:boost:cygwin  
  
These are based on the out-of-date downstream patches in cygwin:  
  
https://cygwin.com/cgit/cygwin-packages/boost/tree/  
  
> Please consider raising new pull requests at https://github.com/chriskohlhoff/asio/pulls.  
  
I looked into this, but rebasing will require changes, and I don't fully understand how these two repos are related.  If it's preferable for this sort of change, please let me know.

---
