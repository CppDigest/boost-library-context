# #138 - CMake tests fail [Open]

> Username: pdimov  
> Created at: 2024-10-17 09:52:17 UTC  
> Updated at: 2024-10-17 11:17:12 UTC  
> Url: https://github.com/boostorg/optional/issues/138  

With  
```  
[ 42%] Building CXX object libs/optional/test/CMakeFiles/boost_optional-optional_test_inplace_factory.dir/optional_test_inplace_factory.cpp.o  
/home/runner/work/boost/boost/libs/optional/test/optional_test_inplace_factory.cpp:21:10: fatal error: boost/utility/in_place_factory.hpp: No such file or directory  
   21 | #include "boost/utility/in_place_factory.hpp"  
      |          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
(https://github.com/boostorg/boost/actions/runs/11381811673/job/31664029157)

---

## Comment 1

> Username: akrzemi1  
> Created at: 2024-10-17 11:17:10 UTC  
> Url: https://github.com/boostorg/optional/issues/138#issuecomment-2419246809  

Working on it.
