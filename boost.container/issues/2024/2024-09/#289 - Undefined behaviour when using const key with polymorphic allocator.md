# #289 - Undefined behaviour when using const key with polymorphic allocator [Closed]

> Username: timrae  
> Created at: 2024-09-10 07:43:41 UTC  
> Updated at: 2024-09-11 19:30:04 UTC  
> Closed at: 2024-09-11 19:26:23 UTC  
> Url: https://github.com/boostorg/container/issues/289  

Hi currently I'm trying to update some code from boost 1.82.0 to 1.86.0 and ran into some undefined behavior introduced by [this change](https://github.com/boostorg/container/commit/20ad12f20e661978e90dc7f36d8ab8ac05e5a5a9#diff-9116f4439192bd392bc0124b167ab74be92831eb4ae4cee1d3700bc46c5f6491R73)  
  
Here's a minimal test case repro:  
  
```c++  
using namespace boost::container::pmr;  
//using element_type = std::pair<std::string_view, int>; // OK  
using element_type = std::pair<const std::string_view, int>; // FAILS  
using map = boost::container::flat_map< std::string_view, int, std::less<>, polymorphic_allocator< element_type > >;  
  
std::vector<element_type> data = { {"1", 1}, {"2", 2}, {"3", 3} };  
map test{ data.begin(), data.end(), std::less<>{}, get_default_resource() }; // Undefined behaviour  
CHECK( test.size() == 3 );  
// CHECK( test["1"] == 1 ); // Compile error  
// CHECK( test["2"] == 2 ); // Compile error  
// CHECK( test["3"] == 3 ); // Compile error  
```  
  
Compiler: `Apple clang version 14.0.3 (clang-1403.0.22.14.1)`

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-09-10 20:45:42 UTC  
> Url: https://github.com/boostorg/container/issues/289#issuecomment-2341979450  

Your element_type is not assignable, how can we expect it to work in a container that needs to move data?

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-09-10 21:00:31 UTC  
> Url: https://github.com/boostorg/container/issues/289#issuecomment-2342002111  

I just noticed the you define:  
  
   //using element_type = std::pair<std::string_view, int>; // OK  
   using element_type = std::pair<const std::string_view, int>; // FAILS  
     
But this is not correct, the value_type of the container is the first one, so that is what the allocator needs to allocate:  
  
https://www.boost.org/doc/libs/1_86_0/doc/html/doxygen/boost_container_header_reference/classboost_1_1container_1_1flat__map.html  
  
_value_type is std::pair<Key,T> (unlike std::map<Key, T> which value_type is std::pair<const Key, T>)._  
  
It seems that somehow the wrong allocator compiles, so maybe I should add a compile-time check when the allocator does not match the expected value_type

---

## Comment 3

> Username: igaztanaga  
> Created at: 2024-09-10 21:53:58 UTC  
> Url: https://github.com/boostorg/container/issues/289#issuecomment-2342073966  

I've added some static checks to test the allocator's value_type here: https://github.com/boostorg/container/commit/49440c3525a2f1136fd9cc0236fc85b2fa411168

---

## Comment 4

> Username: timrae  
> Created at: 2024-09-10 22:05:06 UTC  
> Url: https://github.com/boostorg/container/issues/289#issuecomment-2342090548  

Great thank you! Yes a static check is also what I was imagining.

---

## Comment 5

> Username: timrae  
> Created at: 2024-09-11 06:58:36 UTC  
> Updated at: 2024-09-11 07:35:11 UTC  
> Url: https://github.com/boostorg/container/issues/289#issuecomment-2342815995  

Hi the code above still compiles with the static asserts enabled. I think you also need to static assert in flat_map for the allocator value type not the container value type.

---

## Comment 6

> Username: igaztanaga  
> Created at: 2024-09-11 10:34:35 UTC  
> Url: https://github.com/boostorg/container/issues/289#issuecomment-2343271236  

At least in several compilers, the following example (a complete compilable one, based on your example) does not compile (the static assertion triggers):  
  
```  
#include <boost/container/pmr/flat_map.hpp>  
#include <vector>  
#include <string>  
#ifndef NASSERT  
#  define NASSERT  
#endif  
#include <cassert>  
  
int main()  
{  
   using namespace boost::container::pmr;  
   using element_type = std::pair<std::string, int>; // OK  
   //using element_type = std::pair<const std::string, int>; // FAILS  
   using map = boost::container::flat_map< std::string, int, std::less<>, polymorphic_allocator< element_type > >;  
  
   std::vector<element_type> data = { {"1", 1}, {"2", 2}, {"3", 3} };  
   map test{ data.begin(), data.end(), std::less<>{}, get_default_resource() }; // Undefined behaviour  
   assert(test.size() == 3);  
   assert( test["1"] == 1 );  
   assert( test["2"] == 2 );  
   assert( test["3"] == 3 );  
   return 0;  
}  
```  
  
Using the correct allocator compiles and runs fine.

---

## Comment 7

> Username: timrae  
> Created at: 2024-09-11 10:41:51 UTC  
> Url: https://github.com/boostorg/container/issues/289#issuecomment-2343284532  

Not for me on `Apple clang version 14.0.3 (clang-1403.0.22.14.1)`. The assert here doesn't match the comment, shouldn't it be the same as the other asserts you added in https://github.com/boostorg/container/commit/49440c3525a2f1136fd9cc0236fc85b2fa411168?  
https://github.com/boostorg/container/blob/develop/include/boost/container/flat_map.hpp#L242

---

## Comment 8

> Username: igaztanaga  
> Created at: 2024-09-11 14:33:54 UTC  
> Url: https://github.com/boostorg/container/issues/289#issuecomment-2343857522  

There was an error in my example, you must uncomment the FAILS line and comment the OK line.  
  
The assertion you are looking for is in the implementation detail flat_tree (all flat_xxx containers are wrappers over flat_tree):  
  
https://github.com/boostorg/container/blob/develop/include/boost/container/detail/flat_tree.hpp#L597

---

## Comment 9

> Username: timrae  
> Created at: 2024-09-11 16:06:23 UTC  
> Updated at: 2024-09-11 16:06:49 UTC  
> Url: https://github.com/boostorg/container/issues/289#issuecomment-2344081986  

Hi yes I understand that, but it's not working.  
AFAICT `flat_tree` is created with a [new allocator type](https://github.com/boostorg/container/blob/develop/include/boost/container/flat_map.hpp#L1858) using `impl_pair_t`, which in this case doesn't match the provided allocator type. So you also need to add the static assert to flat_map not just flat_tree.  
  
Adding the static assert to flat_map fixes the problem, not sure why you would be opposed to adding it there as an additional safety mechanism, even if what you say were true?

---

## Comment 10

> Username: igaztanaga  
> Created at: 2024-09-11 16:59:54 UTC  
> Updated at: 2024-09-11 17:00:29 UTC  
> Url: https://github.com/boostorg/container/issues/289#issuecomment-2344206715  

impl_pair_t is the same as pair_t for C++>=11 compilers:  
  
https://github.com/boostorg/container/blob/develop/include/boost/container/flat_map.hpp#L160  
  
Are you using C++03? If not, BOOST_CONTAINER_STD_PAIR_IS_MOVABLE should be defined and impl_pair_t should be std_pair_t. And BOOST_CONTAINER_STD_PAIR_IS_MOVABLE is defined if your compiler has rvalue references.  
  
I am missing something different between or toolset/compiler/version or something

---

## Comment 11

> Username: timrae  
> Created at: 2024-09-11 17:22:50 UTC  
> Url: https://github.com/boostorg/container/issues/289#issuecomment-2344266834  

I'm using c++20  
  
> impl_pair_t is the same as pair_t   
  
Yes but the allocator doesn't use type pair_t if the static assertion fails

---

## Comment 12

> Username: igaztanaga  
> Created at: 2024-09-11 18:05:24 UTC  
> Url: https://github.com/boostorg/container/issues/289#issuecomment-2344347247  

I'm lost. We are not compiling the same code, are you sure you are using the latest develop commit (49440c3525a2f1136fd9cc0236fc85b2fa411168)? This code, passing the wrong allocator, triggers the assertion :  
  
```  
#include <boost/container/pmr/flat_map.hpp>  
#include <vector>  
#include <string>  
#ifndef NASSERT  
#  define NASSERT  
#endif  
#include <cassert>  
  
int main()  
{  
   using namespace boost::container::pmr;  
   //using element_type = std::pair<std::string, int>; // OK  
   using element_type = std::pair<const std::string, int>; // FAILS  
   using map = boost::container::flat_map< std::string, int, std::less<>, polymorphic_allocator< element_type > >;  
  
   std::vector<element_type> data = { {"1", 1}, {"2", 2}, {"3", 3} };  
   map test{ data.begin(), data.end(), std::less<>{}, get_default_resource() }; // Undefined behaviour  
   assert(test.size() == 3);  
   assert(test["1"] == 1);  
   assert(test["2"] == 2);  
   assert(test["3"] == 3);  
   return 0;  
}  
```  
  
Compiling with clang-18 in Linux triggers the assertion:  
  
../../../boost/container/vector.hpp:819:35: error: static assertion failed due to requirement 'dtl::is_same<std::pair<std::basic_string<char, std::char_traits<char>, std::allocator<char>>, int>, std::pair<const std::basic_string<char, std::char_traits<char>, std::allocator<char>>, int>>::value': (dtl::is_same<value_type, typename allocator_traits_t::value_type>::value)  
  819 |    BOOST_CONTAINER_STATIC_ASSERT((dtl::is_same<value_type, typename allocator_traits_t::value_type>::value));  
      |                                   ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
../../../boost/container/detail/workaround.hpp:195:65: note: expanded from macro 'BOOST_CONTAINER_STATIC_ASSERT'  
  195 | #     define BOOST_CONTAINER_STATIC_ASSERT( ... ) static_assert(__VA_ARGS__, #__VA_ARGS__)  
      |                                                                 ^~~~~~~~~~~  
../../../boost/container/detail/flat_tree.hpp:504:21: note: in instantiation of template class 'boost::container::vector<std::pair<std::basic_string<char>, int>, boost::container::pmr::polymorphic_allocator<std::pair<const std::basic_string<char>, int>>>' requested here  
  504 |    typedef typename container_type::allocator_type        allocator_t;  
      |                     ^  
  
[......]  
  
3 errors generated.  
  
    "clang++-18"   -std=c++20 -fvisibility-inlines-hidden -fPIC -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -m64 --target=x86_64-pc-linux  -DBOOST_ALL_NO_LIB=1 -DBOOST_COBALT_USE_STD_PMR=1 -DBOOST_CONTAINER_DYN_LINK=1 -DBOOST_CONTAINER_NO_LIB=1   -I"../../.." -I"../include" -I"/mnt/c/Data/LocalGit/boost/libs/intrusive/include" -I"/mnt/c/Data/LocalGit/boost/libs/move/include"  -c -o "../../../bin.v2/libs/container/test/flat_map_test.test/clang-linux-18/debug/x86_64/cxxstd-20-iso/threading-multi/visibility-hidden/flat_map_test.o" "flat_map_test.cpp"  
      
If you don't get the static assertion, I'm afraid we are not compiling the same code.

---

## Comment 13

> Username: timrae  
> Created at: 2024-09-11 19:26:23 UTC  
> Url: https://github.com/boostorg/container/issues/289#issuecomment-2344538145  

Hi sorry, you're right, thanks for including the full example. Looks like the boost dir I was using had two copies of boost.container, one for include and one for lib, and I had only updated the lib part to https://github.com/boostorg/container/commit/49440c3525a2f1136fd9cc0236fc85b2fa411168 - while my test code was still using the old headers from a previous boost version.

---

## Comment 14

> Username: igaztanaga  
> Created at: 2024-09-11 19:30:03 UTC  
> Url: https://github.com/boostorg/container/issues/289#issuecomment-2344544371  

Glad to hear it. Thanks for the report!
