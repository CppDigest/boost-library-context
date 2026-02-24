# #317 Use __linux__ instead of __linux. [Merged]

> Username: giomasce  
> Created at: 2020-01-03 08:19:47 UTC  
> Updated at: 2020-01-24 18:08:54 UTC  
> Merged at: 2020-01-24 18:08:54 UTC  
> Closed at: 2020-01-24 18:08:54 UTC  
> Url: https://github.com/boostorg/config/pull/317  

The macro __linux is less portable and, for example, is not defined on  
some architectures.  
  
See https://gcc.gnu.org/bugzilla/show_bug.cgi?id=28314.

---
