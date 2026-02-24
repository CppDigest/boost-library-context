# #137 Rename JPEG-s reader_backend::buffer member array to buffer_ [Merged]

> Username: mloskot  
> Created at: 2018-09-19 16:59:44 UTC  
> Updated at: 2018-09-20 10:14:57 UTC  
> Merged at: 2018-09-20 10:14:49 UTC  
> Closed at: 2018-09-20 10:14:49 UTC  
> Url: https://github.com/boostorg/gil/pull/137  

The previous name is too generic and prone to variable shadowing, causing compilation warnings (eg. vector named buffer in JPEG-s `reader::read_rows` method).  
  
### Environment  
  
All relevant information like:  
  
- Compiler version: GCC 8.x  
  
### Tasklist  
  
- [x] All CI builds and checks have passed

---
