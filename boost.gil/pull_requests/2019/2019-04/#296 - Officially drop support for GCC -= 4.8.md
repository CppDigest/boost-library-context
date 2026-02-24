# #296 Officially drop support for GCC <= 4.8 [Merged]

> Username: mloskot  
> Created at: 2019-04-28 00:12:38 UTC  
> Updated at: 2019-04-28 14:15:56 UTC  
> Merged at: 2019-04-28 13:50:57 UTC  
> Closed at: 2019-04-28 13:50:57 UTC  
> Url: https://github.com/boostorg/gil/pull/296  

Although GCC 4.8 is labelled as C++11 compatible, the reasons are:  
  
* GCC 4.8 is buggy enough to prevent reasonable use of Boost.MP11  
* GCC 4.8 is dying  
  
### References  
  
* Closes #282  
* https://lists.boost.org/boost-gil/2019/04/0185.php  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---
