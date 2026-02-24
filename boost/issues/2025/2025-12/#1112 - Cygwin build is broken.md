# #1112 - Cygwin build is broken [Open]

> Username: corngood  
> Created at: 2025-12-18 17:50:21 UTC  
> Updated at: 2025-12-18 17:50:58 UTC  
> Url: https://github.com/boostorg/boost/issues/1112  

I have a series of PRs required to fix cygwin builds of boost.  There's one for each of the submodule changes here:  
  
https://github.com/boostorg/boost/compare/master...corngood:boost:cygwin  
  
These are based on the out-of-date downstream patches in cygwin:  
  
https://cygwin.com/cgit/cygwin-packages/boost/tree/  
  
This issue is to track them in one place:  
  
- https://github.com/boostorg/asio/pull/463  
- https://github.com/boostorg/context/pull/322  
- https://github.com/boostorg/process/pull/533  
- https://github.com/boostorg/stacktrace/pull/219  
- https://github.com/boostorg/system/pull/137
