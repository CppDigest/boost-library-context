# #17 - Compile does not fail as expected gcc-7 cxxstd=17 [Closed]

> Username: jeking3  
> Created at: 2018-11-04 22:49:22 UTC  
> Updated at: 2019-01-06 16:03:58 UTC  
> Closed at: 2019-01-06 16:03:58 UTC  
> Url: https://github.com/boostorg/contract/issues/17  

Fails (as expected) cxxstd=14 but the build seems to succeed in cxxstd=17 mode.  
https://api.travis-ci.org/v3/job/450610134/log.txt  
```  
boost@fe85d9f58fab:/boost/libs/contract/test$ ../../../b2 toolset=gcc cxxstd=14 specify-auto_error  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes  
...patience...  
...patience...  
...found 2491 targets...  
...updating 8 targets...  
gcc.compile.c++ ../../../bin.v2/libs/contract/test/specify-auto_error.test/gcc-7.3/debug/cxxstd-14-iso/visibility-hidden/specify-auto_error.o  
specify/auto_error.cpp: In function 'int main()':  
specify/auto_error.cpp:12:40: error: 'boost::contract::specify_precondition_old_postcondition_except<VR>::specify_precondition_old_postcondition_except(const boost::contract::specify_precondition_old_postcondition_except<VR>&) [with VirtualResult = void]' is private within this context  
     auto c = boost::contract::function(); // Error (can't use auto).  
                                        ^  
In file included from ../../../boost/contract/function.hpp:15:0,  
                 from specify/auto_error.cpp:9:  
../../../boost/contract/core/specify.hpp:618:9: note: declared private here  
         specify_precondition_old_postcondition_except,  
         ^  
../../../boost/contract/core/specify.hpp:52:13: note: in definition of macro 'BOOST_CONTRACT_SPECIFY_CLASS_IMPL_'  
             class_type(class_type const& other) : cond_(other.cond_) {} \  
             ^~~~~~~~~~  
(failed-as-expected) ../../../bin.v2/libs/contract/test/specify-auto_error.test/gcc-7.3/debug/cxxstd-14-iso/visibility-hidden/specify-auto_error.o  
**passed** ../../../bin.v2/libs/contract/test/specify-auto_error.test/gcc-7.3/debug/cxxstd-14-iso/visibility-hidden/specify-auto_error.test  
...updated 8 targets...  
boost@fe85d9f58fab:/boost/libs/contract/test$ ../../../b2 toolset=gcc cxxstd=17 specify-auto_error  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes  
...patience...  
...patience...  
...found 2491 targets...  
...updating 5 targets...  
gcc.compile.c++ ../../../bin.v2/libs/contract/test/specify-auto_error.test/gcc-7.3/debug/cxxstd-17-iso/visibility-hidden/specify-auto_error.o  
...failed gcc.compile.c++ ../../../bin.v2/libs/contract/test/specify-auto_error.test/gcc-7.3/debug/cxxstd-17-iso/visibility-hidden/specify-auto_error.o...  
...removing ../../../bin.v2/libs/contract/test/specify-auto_error.test/gcc-7.3/debug/cxxstd-17-iso/visibility-hidden/specify-auto_error.o  
...failed updating 1 target...  
...skipped 2 targets...  
...updated 2 targets...  
```

---

## Comment 1

> Username: lcaminiti  
> Created at: 2018-12-22 13:29:10 UTC  
> Url: https://github.com/boostorg/contract/issues/17#issuecomment-449570286  

This is a known, expected, and documented behavior on C++17. I disabled this test on C++17 for some compilers but as more C++17 compilers become available this expected failure will show more frequently. I will change the test to pass in this case on all C++17 so this won't be a problem anymore.

---

## Comment 2

> Username: lcaminiti  
> Created at: 2019-01-06 16:03:58 UTC  
> Url: https://github.com/boostorg/contract/issues/17#issuecomment-451752957  

fixed these tests in develop branch for C++17 and above (will merge into master for next boost release)
