# #1101 - -Wno-deprecated-declarations needed to compile boost-1.89 with -Werror on [Open]

> Username: dsatagaj  
> Created at: 2025-11-19 14:30:31 UTC  
> Updated at: 2025-11-19 20:39:23 UTC  
> Url: https://github.com/boostorg/boost/issues/1101  

When upgrading from boost-1.86 to 1.89, got the following error when building my files that include boost headers:  
  
```  
In file included from src/ComputeStats.cpp:18:  
In file included from /dsat/boost/rhel8_intel64_icpx/include/boost/multi_array.hpp  
In file included from /dsat/boost/rhel8_intel64_icpx/include/boost/multi_array/base.hpp  
/dsat/boost/rhel8_intel64_icpx/include/boost/multi_array/storage_order.hpp:51:18: error: 'assign' is deprecated: please use 'fill' instead [-Werror, -Wdeprecated-decratations]  
   51 |   ascending_.assign(true);  
        |  
/dsat/boost/rhel8_intel64_icpx/include/boost/multi_array/multi_array_ref.hpp:113:18: note: in instantiation of member function 'boost:general_storage_order<2>::general_storage_order' requested here  
  113 |   base_(base), storage_(c_storage_order()) {  
         |  
....  
/dsat/boost/rhel8_intel64_icpx/include/boost/array.hpp:193:9: note: 'assign' has been explicitly marked deprecated here  
 193 |   BOOST_DEPRECATED( "please use 'fill' instead" )  
        |  
/dsat/boost/rhel8_intel64_icpx/include/boost/config/compiler/clang.hpp:346:46: note: expanded from macro 'BOOST_DEPRECATED'  
 346 | #define BOOST_DEPRECATED(msg) __attribute__((deprecated(msg)))  
1 error generated  
```  
  
I can work around this by adding -Wno-deprecated-declarations to my cxx flags, but since this is internal to boost, I think it should be addressed. Thank you!

---

## Comment 1

> Username: dsatagaj  
> Created at: 2025-11-19 20:39:23 UTC  
> Url: https://github.com/boostorg/boost/issues/1101#issuecomment-3554546155  

For reference, this is using RHEL8 with the intel compiler (2025.2.1).
