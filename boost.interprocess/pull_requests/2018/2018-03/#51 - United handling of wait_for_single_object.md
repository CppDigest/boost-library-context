# #51 United handling of wait_for_single_object [Merged]

> Username: Vizor  
> Created at: 2018-03-08 15:11:37 UTC  
> Updated at: 2019-03-04 14:40:03 UTC  
> Merged at: 2019-03-04 14:39:43 UTC  
> Closed at: 2019-03-04 14:39:43 UTC  
> Url: https://github.com/boostorg/interprocess/pull/51  

United handling of wait_for_single_object (WaitForSingleObject).  
Fixes #9284.

---

## Comment 1

> Username: 363568233  
> Created_at: 2018-12-17 08:11:19 UTC  
> Url: https://github.com/boostorg/interprocess/pull/51#issuecomment-447756857  

“winapi_wrapper_timed_wait_for_single_object”: could not find in	boost\interprocess\sync\windows\winapi_wrapper_common.hpp   
  
should add function declaration:  
bool winapi_wrapper_timed_wait_for_single_object(void *handle, const boost::posix_time::ptime &abs_time);  
before inline void winapi_wrapper_wait_for_single_object(void *handle)

---

## Comment 2

> Username: Vizor  
> Created_at: 2019-01-22 14:16:36 UTC  
> Url: https://github.com/boostorg/interprocess/pull/51#issuecomment-456414415  

Fixed.

---

## Comment 3

> Username: igaztanaga  
> Created_at: 2019-03-04 14:40:03 UTC  
> Url: https://github.com/boostorg/interprocess/pull/51#issuecomment-469275749  

Many thanks for the patch.

---
