# #74 Added targets /boost//log and /boost//log_setup [Merged]

> Username: tom-bola  
> Created at: 2015-04-29 14:24:31 UTC  
> Updated at: 2021-10-02 22:20:27 UTC  
> Merged at: 2015-05-05 07:08:35 UTC  
> Closed at: 2015-05-05 07:08:35 UTC  
> Url: https://github.com/boostorg/build/pull/74  

In order to link against boost log, I always used to manually add the targets /boost//log and /boost//log_setup, since they are not declared in boost.jam. I was wondering why the library is not supported by boost.build by default. When there is a reason for this, I'm happy to learn about it. Otherwise, the pull request should only be a minor issue.

---
