# #324 Apply the rule of three in memory_based_2d_locator and iterator_from_2d [Merged]

> Username: mloskot  
> Created at: 2019-07-06 18:43:57 UTC  
> Updated at: 2019-07-07 11:59:22 UTC  
> Merged at: 2019-07-07 11:58:05 UTC  
> Closed at: 2019-07-07 11:58:05 UTC  
> Url: https://github.com/boostorg/gil/pull/324  

Add copy assignment operator to `memory_based_2d_locator` and `iterator_from_2d`.  
Add copy constructor to `image_view` which already has copy assignment operator.  
  
Fix GCC 9 warning [-Wdeprecated-copy](https://gcc.gnu.org/onlinedocs/gcc-9.1.0/gcc/C_002b_002b-Dialect-Options.html#index-Wdeprecated-copy) that:  
  
> implicit declaration of a copy constructor or copy assignment operator  
> is deprecated if the class has a user-provided copy constructor or  
> copy assignment operator, in C++11 and up."  
  
### References  
  
- https://en.wikipedia.org/wiki/Rule_of_three_(C%2B%2B_programming)  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-07-07 11:57:49 UTC  
> Url: https://github.com/boostorg/gil/pull/324#issuecomment-508993748  

This is fairly trivial change, merging.

---
