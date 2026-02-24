# #50 Add a workaround for clang 3.0 not supporting decltype in base class list [Merged]

> Username: Lastique  
> Created at: 2017-09-19 11:57:06 UTC  
> Updated at: 2017-10-26 17:49:13 UTC  
> Merged at: 2017-10-26 17:34:02 UTC  
> Closed at: 2017-10-26 17:34:02 UTC  
> Url: https://github.com/boostorg/type_traits/pull/50  

Use an additional proxy class to define the base class using decltype in a typedef, which is supported by the compiler, judging by Boost.Config tests.  
  
I haven't tested it locally, but I hope it should fix this failure:  
  
http://www.boost.org/development/tests/develop/developer/output/teeks99-02-dc3-0-11-Docker-64on64-boost-bin-v2-libs-type_traits-test-is_nothrow_swappable_test-test-clang-gnu-linux-3-0~c++11-debug.html

---
