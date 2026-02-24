# #467 Always build test/extension/io//simple if format libraries are available [Merged]

> Username: mloskot  
> Created at: 2020-03-24 23:36:48 UTC  
> Updated at: 2020-03-25 22:05:37 UTC  
> Merged at: 2020-03-25 09:30:18 UTC  
> Closed at: 2020-03-25 09:30:19 UTC  
> Url: https://github.com/boostorg/gil/pull/467  

### Description  
  
This is part of efforts removing use of compile-time configuration macros  
  - `BOOST_GIL_IO_TEST_ALLOW_READING_IMAGES`  
  - `BOOST_GIL_IO_TEST_ALLOW_WRITING_IMAGES`  
  
### References  
  
- The idea of getting rid of the macros was introduce here https://lists.boost.org/boost-gil//2019/07/0279.php  
- #464  
  
### Tasklist  
  
- [x] Ensure all CI builds pass

---
