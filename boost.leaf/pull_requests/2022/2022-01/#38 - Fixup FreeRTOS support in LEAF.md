# #38 Fixup FreeRTOS support in LEAF [Merged]

> Username: kammce  
> Created at: 2022-01-04 18:12:18 UTC  
> Updated at: 2022-01-04 19:12:07 UTC  
> Merged at: 2022-01-04 19:02:10 UTC  
> Closed at: 2022-01-04 19:02:10 UTC  
> Url: https://github.com/boostorg/leaf/pull/38  

- In config.hpp defining BOOST_LEAF_TLS_FREERTOS should also define  
  BOOST_LEAF_NO_THREADS in order to remove the usage of std::thread in  
  the rest of the project.  
- Remove ";" from BOOST_LEAF_TLS_INDEX_TYPE macro  
- Use correct FreeRTOS API for setting and reading TLS array elements

---
