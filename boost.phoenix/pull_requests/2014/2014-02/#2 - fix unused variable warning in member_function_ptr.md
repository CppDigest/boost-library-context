# #2 fix unused variable warning in member_function_ptr [Merged]

> Username: mgaunard  
> Created at: 2014-02-09 11:38:25 UTC  
> Updated at: 2014-02-09 13:23:30 UTC  
> Merged at: 2014-02-09 13:21:46 UTC  
> Closed at: 2014-02-09 13:21:46 UTC  
> Url: https://github.com/boostorg/phoenix/pull/2  

With clang, a warning is emitted because 'rhs' is not used.

---

## Comment 1

> Username: fletchjp  
> Created_at: 2014-02-09 13:23:30 UTC  
> Url: https://github.com/boostorg/phoenix/pull/2#issuecomment-34573614  

Thanks, got it and onto develop.  John  
  
---  
  
From: Mathias Gaunard [notifications@github.com]  
Sent: 09 February 2014 11:38  
To: boostorg/phoenix  
Subject: [phoenix] fix unused variable warning in member_function_ptr (#2)  
  
With clang, a warning is emitted because 'rhs' is not used.  
  
---  
  
You can merge this Pull Request by running  
  
  git pull https://github.com/mgaunard/phoenix develop  
  
Or view, comment on, or merge it at:  
  
  https://github.com/boostorg/phoenix/pull/2  
  
Commit Summary  
-   fix unused variable warning in member_function_ptr  
  
File Changes  
-   M include/boost/phoenix/bind/detail/member_function_ptr.hpphttps://github.com/boostorg/phoenix/pull/2/files#diff-0 (2)  
  
Patch Links:  
-   https://github.com/boostorg/phoenix/pull/2.patch  
-   https://github.com/boostorg/phoenix/pull/2.diff  
  
—  
Reply to this email directly or view it on GitHubhttps://github.com/boostorg/phoenix/pull/2.

---
