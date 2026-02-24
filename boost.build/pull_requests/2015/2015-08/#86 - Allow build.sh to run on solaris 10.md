# #86 Allow build.sh to run on solaris 10 [Merged]

> Username: haus  
> Created at: 2015-08-10 19:40:55 UTC  
> Updated at: 2021-10-02 22:20:25 UTC  
> Merged at: 2015-08-11 13:49:07 UTC  
> Closed at: 2015-08-11 13:49:07 UTC  
> Url: https://github.com/boostorg/build/pull/86  

This PR contains two fixes for solaris 10. First, it updates some subshell invocations to use `` instead of $(), which is not valid in sh. Second, it replaces two invocations of which with test_path, because which always returns 0 on solaris.

---
