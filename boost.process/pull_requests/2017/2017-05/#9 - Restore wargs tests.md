# #9 Restore wargs tests [Merged]

> Username: cdglove  
> Created at: 2017-05-10 12:55:39 UTC  
> Updated at: 2017-05-10 12:59:31 UTC  
> Merged at: 2017-05-10 12:59:31 UTC  
> Closed at: 2017-05-10 12:59:31 UTC  
> Url: https://github.com/boostorg/process/pull/9  

I independently stumbled across the unquoted wargs with spaces bug, and while fixing it, also found that the wargs unit test was 1/2 disabled. I discarded my fix and pulled from https://github.com/klemens-morgenstern/boost-process, then removed the return statements -- that's why both changes show up in this PR.

---
