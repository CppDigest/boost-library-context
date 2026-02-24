# #54 Install specific version of fglrx on Travis-CI [Merged]

> Username: ddemidov  
> Created at: 2014-02-05 12:27:30 UTC  
> Updated at: 2014-02-05 16:44:59 UTC  
> Merged at: 2014-02-05 16:41:55 UTC  
> Closed at: 2014-02-05 16:41:55 UTC  
> Url: https://github.com/boostorg/compute/pull/54  

The current version that gets installed automatically is broken in the  
sense that it does not work on a CPU in the absence of a GPU (e.g. see  
https://travis-ci.org/ddemidov/vexcl/jobs/18265418#L276).  
  
This installs version 2:8.960-0ubuntu1 which does work.

---
