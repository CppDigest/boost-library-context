# #129 Add C++11 requirements to build files [Merged]

> Username: Flamefire  
> Created at: 2024-08-16 09:57:35 UTC  
> Updated at: 2024-08-25 12:44:20 UTC  
> Merged at: 2024-08-16 18:26:05 UTC  
> Closed at: 2024-08-16 18:26:05 UTC  
> Url: https://github.com/boostorg/optional/pull/129  

As the library now requires C++11 since https://github.com/boostorg/optional/commit/82d08adf119d9fd2f99fe02d25ff4c5895bb4a6e the tests e.g. on CI fail with C++03.  
  
Add the C++11 requirement to Jamefile and CMakeLists. The list of features in the Jamfile is mirroring the condition leading to the explicit error in[ `boost/none_t.hpp`](https://github.com/boostorg/optional/blob/develop/include/boost/none_t.hpp#L18-L19)  
  
cc @pdimov for the GHA fix after 5c34c54b05923ee9bba9957f2fa68a9027fe628e

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-08-16 21:32:42 UTC  
> Url: https://github.com/boostorg/optional/pull/129#issuecomment-2294340809  

Should update meta/libraries.json as well.

---

## Comment 2

> Username: akrzemi1  
> Created_at: 2024-08-16 22:14:38 UTC  
> Url: https://github.com/boostorg/optional/pull/129#issuecomment-2294378622  

Thanks. Fixed in develop.

---
