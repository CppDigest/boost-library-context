# #145 Skip link=shared, runtime_link=static instead of aborting the build [Merged]

> Username: pdimov  
> Created at: 2017-06-30 16:09:09 UTC  
> Updated at: 2017-10-27 03:07:31 UTC  
> Merged at: 2017-07-05 15:22:06 UTC  
> Closed at: 2017-07-05 15:22:06 UTC  
> Url: https://github.com/boostorg/boost/pull/145  

This allows f.ex. `b2 stage --with-system --with-filesystem variant=debug,release link=static,shared runtime-link=static,shared` to work.

---
