# #199 Fix build errors with stdlib=sun-stlport on Linux  [Merged]

> Username: mkartashev  
> Created at: 2017-05-22 14:42:56 UTC  
> Updated at: 2021-10-02 22:18:33 UTC  
> Merged at: 2017-06-27 18:39:47 UTC  
> Closed at: 2017-06-27 18:39:47 UTC  
> Url: https://github.com/boostorg/build/pull/199  

For sun-stlport, add -compat=5 to the command line in order to avoid build   
errors on the platforms where it is not the default (like on Linux). Without  
this additional option, the compiler exits with an error because  
-library=stlport4 only works with the Sun ABI (-compat=5), while the default  
might be GNU ABI (-compat=g).  
  
Do the same for the stdcxx library that is also available only in -compat=5  
mode. This change is not strictly necessary as the stdcxx library is only  
available on Solaris, where the default is -compat=5 anyway. It has been added  
as a precaution.

---

## Comment 1

> Username: mkartashev  
> Created_at: 2017-05-22 15:00:17 UTC  
> Url: https://github.com/boostorg/build/pull/199#issuecomment-303126047  

Corresponding ticket is https://svn.boost.org/trac/boost/ticket/13042

---
