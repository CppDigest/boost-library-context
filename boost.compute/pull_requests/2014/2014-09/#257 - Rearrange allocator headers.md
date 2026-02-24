# #257 Rearrange allocator headers [Merged]

> Username: kylelutz  
> Created at: 2014-09-06 16:55:33 UTC  
> Updated at: 2014-09-06 17:21:06 UTC  
> Merged at: 2014-09-06 17:21:05 UTC  
> Closed at: 2014-09-06 17:21:05 UTC  
> Url: https://github.com/boostorg/compute/pull/257  

This moves the allocator headers from 'container' to a new  
top-level 'allocator' directory.  
  
Also renames allocator<T> to buffer_allocator<T>.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-09-06 17:08:11 UTC  
> Url: https://github.com/boostorg/compute/pull/257#issuecomment-54720847  

[![Coverage Status](https://coveralls.io/builds/1177840/badge)](https://coveralls.io/builds/1177840)  
  
Coverage increased (+0.0%) when pulling **94d182d47d0913a8e412462124be52d689ad69d9 on rearrange-allocators** into **d8704fd586ec220b1c0954b3ea36475511e317d0 on develop**.

---
