# #71 Add cygwin, cygwin64, and MSVC2017 strict builds to AppVeyor [Merged]

> Username: jeking3  
> Created at: 2018-11-11 15:40:18 UTC  
> Updated at: 2019-02-10 13:06:28 UTC  
> Merged at: 2019-02-10 13:06:28 UTC  
> Closed at: 2019-02-10 13:06:28 UTC  
> Url: https://github.com/boostorg/regex/pull/71  

While this is related to #64 it simply restricts the linking to avoid that issue.  It does not resolve #64, but it does provide a solution for regex and cygwin builds.  I also updated the /std selectors to use Boost.Build since that support has been added.

---
