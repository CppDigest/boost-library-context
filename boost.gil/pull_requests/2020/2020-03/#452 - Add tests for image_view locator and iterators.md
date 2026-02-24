# #452 Add tests for image_view locator and iterators [Merged]

> Username: mloskot  
> Created at: 2020-03-16 00:44:05 UTC  
> Updated at: 2020-03-16 23:31:47 UTC  
> Merged at: 2020-03-16 23:31:41 UTC  
> Closed at: 2020-03-16 23:31:42 UTC  
> Url: https://github.com/boostorg/gil/pull/452  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Updated follows PR #450 by @simmplecoder as mentioned in https://github.com/boostorg/gil/pull/450#pullrequestreview-374812456 extending the tests coverage and still includes test case for fix of issue #432  
  
Commented on UB if specified increment advances non-incrementable iterator.  
Added `TODO` comments asking about clarification of valid pixel range specification for end iterators, especially of empty view (image).  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass

---
