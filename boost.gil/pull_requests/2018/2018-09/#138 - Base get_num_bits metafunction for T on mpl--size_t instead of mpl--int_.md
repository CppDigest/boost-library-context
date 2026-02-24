# #138 Base get_num_bits metafunction for T on mpl::size_t instead of mpl::int_ [Merged]

> Username: mloskot  
> Created at: 2018-09-19 17:00:44 UTC  
> Updated at: 2018-09-23 19:08:33 UTC  
> Merged at: 2018-09-23 19:08:29 UTC  
> Closed at: 2018-09-23 19:08:29 UTC  
> Url: https://github.com/boostorg/gil/pull/138  

Previously used `mpl::int_` assumes signed integer, while `sizeof(T)` is unsigned and compiler issues warning.  
  
### Environment  
  
All relevant information like:  
  
- Compiler version: GCC 8.x  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---
