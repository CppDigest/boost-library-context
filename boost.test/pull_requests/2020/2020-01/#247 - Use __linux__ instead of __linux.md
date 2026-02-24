# #247 Use __linux__ instead of __linux. [Merged]

> Username: giomasce  
> Created at: 2020-01-03 08:15:59 UTC  
> Updated at: 2020-04-21 14:23:37 UTC  
> Merged at: 2020-03-29 14:53:43 UTC  
> Closed at: 2020-03-29 14:53:43 UTC  
> Url: https://github.com/boostorg/test/pull/247  

The macro __linux is less portable and, for example, is not defined on  
some architectures.  
  
See https://gcc.gnu.org/bugzilla/show_bug.cgi?id=28314.

---

## Comment 1

> Username: raffienficiaud  
> Created_at: 2020-03-29 16:39:21 UTC  
> Url: https://github.com/boostorg/test/pull/247#issuecomment-605664127  

In master, thanks!

---
