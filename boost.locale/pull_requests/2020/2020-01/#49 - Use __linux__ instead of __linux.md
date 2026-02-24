# #49 Use __linux__ instead of __linux. [Closed]

> Username: giomasce  
> Created at: 2020-01-03 08:09:44 UTC  
> Updated at: 2022-06-04 09:50:40 UTC  
> Closed at: 2022-06-04 09:50:40 UTC  
> Url: https://github.com/boostorg/locale/pull/49  

The macro __linux is less portable and, for example, is not defined on  
some architectures.  
  
See https://gcc.gnu.org/bugzilla/show_bug.cgi?id=28314.

---
