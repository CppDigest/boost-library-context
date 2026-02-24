# #28 Poison BOOST_DESCRIBE_ENUM in class scope [Merged]

> Username: ecatmur  
> Created at: 2021-12-21 17:53:43 UTC  
> Updated at: 2022-02-19 01:46:58 UTC  
> Merged at: 2022-02-19 01:46:58 UTC  
> Closed at: 2022-02-19 01:46:58 UTC  
> Url: https://github.com/boostorg/describe/pull/28  

using `namespace` keyword (is there anything better that can appear at namespace scope, not at class scope)?  
try to inform the user that they should use `BOOST_DESCRIBE_NESTED_ENUM` instead  
  
Motivation: we found that people were using `BOOST_DESCRIBE_ENUM` in nested context and getting confused when it silently failed. This is particularly problematic because nested enums tend to not be `enum class` so things like `std::ostream<<` will still work, so we were getting unexpected behavior at runtime (our serialization library prefers Boost.Describe but will fall back to iostreams).

---

## Comment 1

> Username: ecatmur  
> Created_at: 2021-12-21 18:45:52 UTC  
> Url: https://github.com/boostorg/describe/pull/28#issuecomment-999011507  

test output:  
```  
gcc.compile.c++ bin.v2/libs/describe/test/enum_nested_fail.test/gcc-11/debug/cxxstd-14-iso/threading-multi/visibility-hidden/enum_nested_fail.o  
In file included from libs/describe/test/enum_nested_fail.cpp:5:  
./boost/describe/enum.hpp:74:5: error: expected unqualified-id before ‘namespace’  
   74 |     namespace should_use_BOOST_DESCRIBE_NESTED_ENUM {} \  
      |     ^~~~~~~~~  
libs/describe/test/enum_nested_fail.cpp:15:5: note: in expansion of macro ‘BOOST_DESCRIBE_ENUM’  
   15 |     BOOST_DESCRIBE_ENUM(E1);  
      |     ^~~~~~~~~~~~~~~~~~~  
(failed-as-expected) bin.v2/libs/describe/test/enum_nested_fail.test/gcc-11/debug/cxxstd-14-iso/threading-multi/visibility-hidden/enum_nested_fail.o  
```  
  
and  
  
```  
gcc.compile.c++ bin.v2/libs/describe/test/nested_enum_fail.test/gcc-11/debug/cxxstd-14-iso/threading-multi/visibility-hidden/nested_enum_fail.o  
In file included from libs/describe/test/nested_enum_fail.cpp:5:  
./boost/describe/enum.hpp:82:33: error: ‘friend’ used outside of class  
   82 |     BOOST_DESCRIBE_MAYBE_UNUSED friend BOOST_DESCRIBE_ENUM_BEGIN(E) \  
      |                                 ^~~~~~  
libs/describe/test/nested_enum_fail.cpp:14:1: note: in expansion of macro ‘BOOST_DESCRIBE_NESTED_ENUM’  
   14 | BOOST_DESCRIBE_NESTED_ENUM(E1);  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~~  
(failed-as-expected) bin.v2/libs/describe/test/nested_enum_fail.test/gcc-11/debug/cxxstd-14-iso/threading-multi/visibility-hidden/nested_enum_fail.o  
```

---
