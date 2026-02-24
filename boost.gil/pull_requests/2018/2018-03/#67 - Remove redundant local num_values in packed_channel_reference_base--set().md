# #67 Remove redundant local num_values in packed_channel_reference_base::set() [Merged]

> Username: mloskot  
> Created at: 2018-03-28 22:14:11 UTC  
> Updated at: 2018-03-31 22:27:36 UTC  
> Merged at: 2018-03-31 22:27:33 UTC  
> Closed at: 2018-03-31 22:27:33 UTC  
> Url: https://github.com/boostorg/gil/pull/67  

Pre-defined packed_channel_reference_base::set::num_values already  
represents the value.  
Fixes warning C4458: declaration of 'num_values' hides class member  
  
### Tasklist  
  
- [x] All CI builds and checks have passed  
  
### Environment  
  
All relevant information like:  
- Compiler version: VS2017

---
