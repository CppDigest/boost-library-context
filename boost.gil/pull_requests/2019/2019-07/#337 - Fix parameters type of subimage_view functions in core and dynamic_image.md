# #337 Fix parameters type of subimage_view functions in core and dynamic_image [Merged]

> Username: mloskot  
> Created at: 2019-07-16 00:37:07 UTC  
> Updated at: 2019-07-16 11:05:53 UTC  
> Merged at: 2019-07-16 11:05:49 UTC  
> Closed at: 2019-07-16 11:05:49 UTC  
> Url: https://github.com/boostorg/gil/pull/337  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
<!-- What does this pull request do? -->  
  
Align with `subsampled_view` overloads accepting `point_t` and `coord_t`  
Both types are based on `std::ptrdiff_t`. Fixes compilation warnings.  
Tidy up `subimage_view` and `subsampled_view` with trailing return.  
  
Add tests for `subimage_view` in core and dynamic_image extension.  
  
_Possibly more important feature of this PR is addition of first tests for the dynamic_image extension, hurray! :)_  
  
------  
  
Test fixtures and utilities in core tests are moved around for easier re-use in extensions tests.  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve  
  
------  
  
Next, I'm planning to add test for the `subsampled_view`-s.

---
