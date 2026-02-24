# #25 - Warning about conversion to unsigned int from int [Closed]

> Username: joaquintides  
> Created at: 2022-07-12 10:17:57 UTC  
> Updated at: 2022-10-27 11:48:25 UTC  
> Closed at: 2022-10-27 11:48:25 UTC  
> Url: https://github.com/boostorg/container_hash/issues/25  

GCC 4.6 [complains](https://www.boost.org/development/tests/master/developer/output/teeks99-dkr-mg4-6-0x-boost-bin-v2-libs-unordered-test-assign_exception_tests-test-gcc-4-6~c++0x-debug-debug-symbols-off-threading-multi-visibility-hidden.html) that:  
  
```  
../boost/container_hash/hash.hpp: In function 'std::size_t boost::hash_detail::hash_value_signed(T)':  
../boost/container_hash/hash.hpp:273:81: error: conversion to 'unsigned int' from 'int' may change the sign of the result [-Werror=sign-conversion]  
../boost/container_hash/hash.hpp: In function 'std::size_t boost::hash_detail::hash_value_unsigned(T)':  
../boost/container_hash/hash.hpp:294:81: error: conversion to 'unsigned int' from 'int' may change the sign of the result [-Werror=sign-conversion]  
```  
I've taken a look at the `develop` branch and saw no trace of a `hash_value_signed` function, so I wonder if the branch was meant  to be merged to `master` before 1.80 (probably too late now).

---

## Comment 1

> Username: pdimov  
> Created at: 2022-07-12 11:48:03 UTC  
> Url: https://github.com/boostorg/container_hash/issues/25#issuecomment-1181661176  

No, it wasn't meant to. It's a fairly disruptive change, so I want to leave it on develop for a while.

---

## Comment 2

> Username: pdimov  
> Created at: 2022-10-27 11:48:25 UTC  
> Url: https://github.com/boostorg/container_hash/issues/25#issuecomment-1293407983  

The develop branch has been merged now, so this should be resolved.
